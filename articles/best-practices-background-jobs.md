<properties
   pageTitle="Guide des travaux d’arrière-plan | Microsoft Azure"
   description="Conseils sur l’arrière-plan des tâches qui s’exécutent indépendamment de l’interface utilisateur."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="masashin"/>

# <a name="background-jobs-guidance"></a>Guide des tâches en arrière-plan

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Vue d’ensemble

De nombreux types d’applications requièrent des tâches en arrière-plan qui s’exécutent indépendamment de l’interface utilisateur (IU). Traitements par lots, les tâches de traitement intensif et les processus longs, tels que des flux de travail sont des exemples. Tâches en arrière-plan qui peuvent être exécutées sans nécessiter l’intervention de l’utilisateur, l’application peut démarrer la tâche et puis continuer à traiter les demandes interactifs des utilisateurs. Cela peut aider à réduire la charge sur l’application de l’interface utilisateur, ce qui peut améliorer la disponibilité et réduire le temps de réponse interactif.

Par exemple, si une application est requis pour générer des miniatures d’images qui sont téléchargées par les utilisateurs, il peut effectuer cette opération en tant que tâche d’arrière-plan et enregistrer la miniature de stockage lorsqu’il est complet, sans que l’utilisateur de devoir attendre le processus pour aboutir. De la même façon, un utilisateur de passer une commande peut initier un flux de travail d’arrière-plan qui traite la commande, alors que l’interface utilisateur permet à l’utilisateur de continuer à naviguer sur l’application web. Lorsque le travail d’arrière-plan est terminé, il peut mettre à jour des données de commandes stockées et envoyer un e-mail à l’utilisateur qui confirme la commande.

Lorsque vous considérez si pour implémenter une tâche en tâche d’arrière-plan, le principal critère est si la tâche peut s’exécuter sans intervention de l’utilisateur et sans l’interface utilisateur ayant besoin d’attendre que la tâche doit être terminée. Les tâches qui nécessitent de l’utilisateur ou l’interface utilisateur à patienter pendant qu’ils sont terminés n’est peut-être pas appropriés en tant que travaux en arrière-plan.

## <a name="types-of-background-jobs"></a>Types de tâches en arrière-plan

Tâches en arrière-plan incluent généralement un ou plusieurs des types de tâches suivants :

- Tâches de consommateurs de ressources processeur, telles que les calculs mathématiques ou de l’analyse du modèle structurel.
- Tâches d’e/S intensives, telles que l’exécution d’une série de transactions de stockage ou de l’indexation des fichiers.
- Traitements par lots, par exemple les mises à jour de données pendant la nuit ou le traitement planifié.
- Workflows longue, Honorer les commandes ou mise en service de systèmes et services.
- Traitement des données sensibles où la tâche est remise à un emplacement plus sécurisé pour le traitement. Par exemple, il pourriez que vous ne souhaitez pas traiter des données sensibles dans une application web. Au lieu de cela, vous pouvez utiliser un modèle de [contrôle d’appels](http://msdn.microsoft.com/library/dn589793.aspx) pour transférer les données à un processus d’arrière-plan isolé qui a accès à l’emplacement protégé.

## <a name="triggers"></a>Déclencheurs

Tâches en arrière-plan peuvent être lancées de différentes manières. Ils appartiennent à l’une des catégories suivantes :

- [**Déclencheurs de piloté par événements**](#event-driven-triggers). La tâche est démarrée en réponse à un événement, généralement une action effectuée par un utilisateur ou d’une étape dans un flux de travail.
- [**Planification pilotée par les déclencheurs**](#schedule-driven-triggers). La tâche est appelée sur un programme basé sur une minuterie. Il peut s’agir d’une planification périodique ou un appel d’One-Off est spécifié pour une date ultérieure.

### <a name="event-driven-triggers"></a>Déclencheurs de piloté par événements

Appel de piloté par événements utilise un déclencheur pour démarrer la tâche d’arrière-plan. Exemples d’utilisation de déclencheurs de piloté par événements :

- L’interface utilisateur ou une autre tâche place un message dans une file d’attente. Le message contient des données relatives à une action qui a eu lieu, comme l’utilisateur de passer une commande. La tâche d’arrière-plan est à l’écoute sur cette file d’attente et détecte l’arrivée d’un nouveau message. Il lit le message et qu’il utilise les données comme entrée pour la tâche d’arrière-plan.
- L’interface utilisateur ou une autre tâche enregistre ou met à jour une valeur dans le stockage. La tâche d’arrière-plan surveille le stockage et détecte les modifications apportées. Il lit les données et l’utilise comme entrée pour la tâche d’arrière-plan.
- L’interface utilisateur ou une autre tâche effectue une demande à un point de terminaison, tel qu’une URI HTTPS, ou une API qui est exposée comme un service web. Il transmet les données sont nécessaire pour terminer la tâche d’arrière-plan dans le cadre de la demande. Le point de terminaison ou de service web appelle la tâche d’arrière-plan, qui utilise les données comme son entrée.

Des exemples de tâches qui sont adaptés à l’appel de piloté par événements incluent des workflows, d’envoyer des informations aux services à distance, envoi de messages électroniques et le provisionnement des nouveaux utilisateurs dans les applications mutualisées, de traitement d’image.

### <a name="schedule-driven-triggers"></a>Planification pilotée par des déclencheurs

Planification pilotée par un appel utilise un timer pour démarrer la tâche d’arrière-plan. Exemples d’utilisation des prévisions pilotées par les déclencheurs sont les suivantes :

- Une minuterie qui s’exécute localement dans l’application ou dans le cadre du système d’exploitation de l’application appelle une tâche en arrière-plan à intervalles réguliers.
- Une minuterie qui s’exécute dans une autre application ou un service de minuteur comme planificateur d’Azure, envoie une demande à un service web ou des API sur une base régulière. L’API ou le service web appelle la tâche en arrière-plan.
- Un processus distinct ou une application démarre une minuterie qui provoque la tâche en arrière-plan à appeler une fois après un délai spécifié, ou à une heure spécifique.

Des exemples de tâches qui conviennent aux prévisions pilotées par l’appel incluent des routines de traitement par lots (par exemple, la mise à jour des listes de produits liés à des utilisateurs en fonction de leur comportement récent), des tâches de routine de traitement de données (telles que la mise à jour d’index ou la génération de résultats cumulés), analyse des données pour les rapports quotidiens, nettoyage de rétention des données et les contrôles de cohérence de données.

Si vous utilisez une tâche pilotée par planification qui doit s’exécuter comme une instance unique, prenez en compte les éléments suivants :

- Si l’instance de calcul qui est en cours d’exécution du planificateur (par exemple, un ordinateur virtuel à l’aide des tâches planifiées de Windows) est mis à l’échelle, vous aurez plusieurs instances du planificateur en cours d’exécution. Il a pu démarrer plusieurs instances de la tâche.
- Si les tâches s’exécutent plus longtemps que la période entre les événements du planificateur, le planificateur peut démarrer une autre instance de la tâche pendant l’exécution.

## <a name="returning-results"></a>Retour de résultats

Tâches en arrière-plan exécutent de façon asynchrone dans un processus distinct, ou même dans un emplacement distinct, à partir de l’interface utilisateur ou le processus qui l’a appelée la tâche en arrière-plan. Dans l’idéal, tâches en arrière-plan sont des opérations « fire and forget » et leur progression de l’exécution n’a aucune incidence sur l’interface utilisateur ou le processus appelant. Cela signifie que le processus appelant n’attend pas la fin des tâches. Par conséquent, il ne peut pas détecter automatiquement lorsque la tâche se termine.

Si vous avez besoin de communiquer avec la tâche appelante pour indiquer la progression ou fin d’une tâche en arrière-plan, vous devez implémenter un mécanisme pour cela. Quelques exemples sont :

- Écrire une valeur d’indicateur de statut dans le stockage accessible à la tâche de l’interface utilisateur ou à l’appelant, qui peut contrôler ou vérifier cette valeur lorsque cela est nécessaire. Autres données de la tâche en arrière-plan doit renvoyer à l’appelant peuvent être placées dans le même emplacement de stockage.
- Établir une file d’attente de la réponse qui écoute l’interface utilisateur ou à l’appelant. La tâche d’arrière-plan peut envoyer des messages à la file d’attente qui indiquent le statut et accomplissement. Données de la tâche en arrière-plan doit renvoyer à l’appelant peuvent être placées dans les messages. Si vous utilisez le Bus des services Azure, vous pouvez utiliser les propriétés **ReplyTo** et **CorrelationId** pour implémenter cette fonctionnalité. Pour plus d’informations, reportez-vous à la section [corrélation dans Service Bus demandé de la messagerie](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Exposent une API ou le point de terminaison à partir de la tâche d’arrière-plan qui peut accéder à l’interface utilisateur ou à l’appelant pour obtenir des informations sur l’état. Données de la tâche en arrière-plan doit renvoyer à l’appelant peuvent être incluses dans la réponse.
- Avoir la tâche d’arrière-plan de rappeler à l’interface utilisateur ou l’appelant via une API pour indiquer l’état à des points prédéfinis, ou à la fin. Il peut s’agir d’événements déclenchés localement ou via un mécanisme de publication et d’abonnement. Données de la tâche en arrière-plan doit renvoyer à l’appelant peuvent être incluses dans la charge utile de demande ou de l’événement.

## <a name="hosting-environment"></a>Environnement d’hébergement

Vous pouvez héberger des tâches en arrière-plan à l’aide d’une gamme de services de la plateforme Azure différente :

- [**WebJobs et applications Web azure**](#azure-web-apps-and-webjobs). Vous pouvez utiliser WebJobs pour exécuter des tâches personnalisées basées sur une plage de différents types de scripts ou de programmes exécutables dans le contexte d’une application web.
- [**Rôles web et worker de Services Cloud azure**](#azure-cloud-services-web-and-worker-roles). Vous pouvez écrire du code dans un rôle qui s’exécute en tâche de fond.
- [**Les Machines virtuelles azure**](#azure-virtual-machines). Si vous avez un service Windows ou que vous souhaitez utiliser le Planificateur de tâches de Windows, il est courant pour héberger vos tâches en arrière-plan sur une machine virtuelle dédiée.
- [**Lot d’azure**](./batch/batch-technical-overview.md). Il s’agit d’un service de plate-forme qui planifie le travail de calcul intensif pour s’exécuter sur une collection managée de machines virtuelles, et pouvez échelle calculer automatiquement les ressources pour répondre aux besoins de vos projets.

Les sections suivantes décrivent chacune de ces options plus en détail et des considérations pour vous aider à choisir l’option appropriée.

## <a name="azure-web-apps-and-webjobs"></a>WebJobs et applications Web azure

Vous pouvez utiliser Azure WebJobs pour exécuter des tâches personnalisées en tant que tâches d’arrière-plan au sein d’une application Web de Azure. WebJobs exécuter dans le contexte de votre application web, comme un processus continu. WebJobs s’exécutent également en réponse à un événement déclencheur d’Azure planificateur ou des facteurs externes, tels que les modifications apportées aux objets BLOB de stockage et les files d’attente. Travaux peuvent être démarrés et arrêtés à la demande et s’arrête. En cas d’échec d’une WebJob en permanence en cours d’exécution, il est automatiquement redémarré. Actions de nouvelle tentative et d’erreur sont configurables.

Lorsque vous configurez un WebJob :

- Si vous souhaitez que la tâche de répondre à un déclencheur événementiel, vous devez le configurer en tant que de **s’exécuter en continu**. Le script ou le programme est stocké dans le dossier nommé site/wwwroot/app_data/travaux/en continu.
- Si vous souhaitez que le travail pour répondre à un déclencheur piloté par programme, vous devez le configurer en tant que de **l’exécuter selon une planification**. Le script ou le programme est stocké dans le dossier nommé site/wwwroot/app_data/travaux/déclenchée.
- Si vous choisissez l’option **Exécuter à la demande** lorsque vous configurez une tâche, il exécute le même code que l’option **exécuter selon une planification** lorsque vous le démarrez.

WebJobs Azure s’exécutent dans le sandbox de l’application web. Cela signifie qu’ils peuvent accéder aux variables d’environnement et partager des informations, telles que les chaînes de connexion avec l’application web. Le travail a accès à l’identificateur unique de l’ordinateur qui exécute la tâche. La chaîne de connexion nommée **AzureWebJobsStorage** fournit l’accès aux files d’attente de stockage Azure, les BLOB et les tables de données d’application et l’accès aux Bus des services de messagerie et de communication. La chaîne de connexion nommée **AzureWebJobsDashboard** fournit l’accès aux fichiers journaux de l’action de la tâche.

WebJobs Azure présentent les caractéristiques suivantes :

- **Sécurité**: WebJobs sont protégés par les informations d’identification de déploiement de l’application web.
- **Types de fichier pris en charge**: vous pouvez définir WebJobs à l’aide de scripts de commandes (.cmd), fichiers de commandes (.bat), PowerShell scripts (.ps1), bash scripts de shell (.sh), les scripts PHP (.php), scripts de Python (.py), le code JavaScript (.js) et des programmes exécutables (.exe, .jar, etc.).
- **Déploiement**: vous pouvez déployer des scripts et exécutables à l’aide du portail Azure, en utilisant le complément [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) pour Visual Studio ou [Visual Studio 2013 Update 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) (vous pouvez créer et déployer avec cette option), à l’aide du [Kit de développement logiciel Azure WebJobs](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md), ou en les copiant directement aux emplacements suivants :
  - Pour a déclenché l’exécution : site/wwwroot/app_data/travaux/déclenchée / {nom du travail}
  - Pour une exécution en continu : site/wwwroot/app_data/travaux/continu / {nom du travail}
- **Enregistrement**: Console.Out est traité (marqué) sous la forme d’informations. Console.Error est considéré comme une erreur. Vous pouvez accéder à des informations de diagnostic et de surveillance à l’aide du portail Azure. Vous pouvez télécharger les fichiers journaux à partir du site. Ils sont enregistrés dans les emplacements suivants :
  - Pour a déclenché l’exécution : Vfs/données/tâches/déclenchée/jobName
  - Pour une exécution en continu : Vfs/données/tâches/continu/jobName
- **Configuration**: vous pouvez configurer les WebJobs à l’aide du portail, l’API REST et PowerShell. Vous pouvez utiliser un fichier de configuration nommé settings.job dans le même répertoire racine en tant que script de travail pour fournir des informations de configuration d’un projet. Par exemple :
  - {« stopping_wait_time » : 60}
  - {« is_singleton » : true}

### <a name="considerations"></a>Considérations relatives à la

- Par défaut, il s’agit d’échelle de WebJobs avec l’application web. Toutefois, vous pouvez configurer les tâches à exécuter sur une instance unique en affectant à la propriété de configuration **is_singleton** la **valeur true**. Instance unique WebJobs sont utiles pour les tâches que vous ne souhaitez pas mettre à l’échelle ou l’exécuter en tant que multiples simultanées instances, comme la réindexation, l’analyse de données et des tâches similaires.
- Pour minimiser l’impact des travaux sur les performances de l’application web, envisagez de créer une instance d’Azure Web App vide dans un nouveau plan de Service d’application pour la ressource ou l’hôte WebJobs qui peut être longue intensive.

### <a name="more-information"></a>Plus d’informations

- [WebJobs d’Azure recommandé des ressources](./app-service-web/websites-webjobs-resources.md) répertorie les nombreuses ressources utiles, téléchargements et des exemples pour WebJobs.

## <a name="azure-cloud-services-web-and-worker-roles"></a>Azure rôles web et worker de Services en nuage

Vous pouvez exécuter les tâches d’arrière-plan dans un rôle web ou dans un rôle de travail distinct. Lorsque vous décidez s’il faut utiliser un rôle worker, tenez compte de l’évolutivité et les exigences de l’élasticité, durée de vie de tâche, relâchez la cadence, la sécurité, une tolérance de pannes, contention, la complexité et l’architecture logique. Pour plus d’informations, consultez [Modèle de Consolidation de ressource de calcul](http://msdn.microsoft.com/library/dn589778.aspx).

Il existe plusieurs façons d’implémenter les tâches d’arrière-plan dans un rôle de Services en nuage :

- Créez une implémentation de la classe **RoleEntryPoint** dans le rôle et utiliser ses méthodes pour exécuter des tâches en arrière-plan. Les tâches exécutées dans le contexte de WaIISHost.exe. La méthode **GetSetting** de la classe **CloudConfigurationManager** leur permet de charger les paramètres de configuration. Pour plus d’informations, consultez [cycle de vie (Services en nuage)](#lifecycle-cloud-services).
- Tâches de démarrage permet d’exécuter des tâches en arrière-plan lorsque l’application démarre. Pour forcer les tâches à continuer à s’exécuter en arrière-plan, affectez la propriété **taskType** **arrière-plan** (si vous ne le faites pas, le processus de démarrage d’application va arrêter et attendre que la tâche se termine). Pour plus d’informations, consultez [exécution de tâches de démarrage dans Azure](./cloud-services/cloud-services-startup-tasks.md).
- Utiliser le SDK WebJobs pour implémenter des tâches en arrière-plan comme WebJobs qui sont lancées comme une tâche de démarrage. Pour plus d’informations, consultez [mise en route avec le Kit de développement logiciel WebJobs Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Une tâche de démarrage permet d’installer un service Windows qui s’exécute une ou plusieurs tâches d’arrière-plan. Vous devez définir la propriété **taskType** à **l’arrière-plan** afin que le service s’exécute en arrière-plan. Pour plus d’informations, consultez [exécution de tâches de démarrage dans Azure](./cloud-services/cloud-services-startup-tasks.md).

### <a name="running-background-tasks-in-the-web-role"></a>Tâches en arrière-plan en cours d’exécution dans le rôle web

Le principal avantage de l’exécution des tâches en arrière-plan dans le rôle web est l’enregistrement dans les coûts d’hébergement, car il n’y a aucun besoin de déployer des rôles supplémentaires.

### <a name="running-background-tasks-in-a-worker-role"></a>Tâches en arrière-plan en cours d’exécution dans un rôle de collaborateur

Exécute des tâches en arrière-plan avec un rôle worker présente plusieurs avantages :

- Il vous permet de gérer la mise à l’échelle séparément pour chaque type de rôle. Par exemple, vous devrez peut-être plusieurs instances d’un rôle web pour supporter la charge en cours, mais moins de cas du rôle Collaborateur qui exécute des tâches en arrière-plan. Par la mise à l’échelle des instances de calcul arrière-plan tâche séparément dans les rôles de l’interface utilisateur, vous pouvez réduire les coûts d’hébergement, tout en maintenant des performances acceptables.
- Il décharge la surcharge de traitement pour les tâches en arrière-plan du rôle web. Le rôle web qui fournit l’interface utilisateur peut rester réactif, et il peut indiquer de que moins d’instances sont nécessaires pour prendre en charge d’un volume de demandes provenant des utilisateurs.
- Il vous permet d’implémenter la séparation des intérêts. Chaque type de rôle peut mettre en œuvre un ensemble spécifique de tâches clairement définies et connexes. Ainsi, la conception et la gestion du code, car il est moins interdépendance de code et des fonctionnalités entre chaque rôle.
- Il peut vous aider à isoler les données et les processus sensibles. Par exemple, les rôles web qui implémentent l’interface utilisateur est inutile d’accéder aux données qui sont gérées et contrôlées par un rôle worker. Cela peut être utile pour le renforcement de la sécurité, notamment lorsque vous utilisez un modèle tel que le [Modèle de contrôle d’appels](http://msdn.microsoft.com/library/dn589793.aspx).  

### <a name="considerations"></a>Considérations relatives à la

Tenez compte des points suivants lorsque vous choisissez comment et où déployer les tâches en arrière-plan lors de l’utilisation de rôles web et worker de Services en nuage :

- Hébergement des tâches d’arrière-plan dans un rôle web existant peut l’économie d’un rôle de travail séparé pour ces tâches en cours d’exécution. Toutefois, il est susceptible d’affecter les performances et la disponibilité de l’application si il existe un conflit de transformation et d’autres ressources. À l’aide d’un rôle de travail séparé empêche le rôle web l’impact des tâches en arrière-plan de longue durée ou nécessitant beaucoup de ressources.
- Si vous hébergez des tâches en arrière-plan à l’aide de la classe **RoleEntryPoint** , vous pouvez facilement déplacer cela à un autre rôle. Par exemple, si vous créez la classe dans un rôle web et que vous décidez ultérieurement que vous devez exécuter les tâches dans un rôle de collaborateur, vous pouvez déplacer l’implémentation de la classe **RoleEntryPoint** dans le rôle de travail.
- Tâches de démarrage sont conçus pour exécuter un programme ou un script. Déploiement d’une tâche en arrière-plan sous la forme d’un programme exécutable peut être plus difficile, surtout si elle requiert également le déploiement des assemblys dépendants. Il peut être plus facile à déployer et à utiliser un script pour définir une tâche en arrière-plan lorsque vous utilisez des tâches de démarrage.
- Les exceptions qui provoquent l’échec d’une tâche en arrière-plan ont un impact différent, en fonction de la manière qu’ils sont hébergés :
  - Si vous utilisez l’approche de la classe **RoleEntryPoint** , une tâche ayant échoué entraînera le rôle à redémarrer pour que la tâche redémarre automatiquement. Cela peut affecter la disponibilité de l’application. Pour éviter ce problème, veillez à inclure des exceptions robuste dans la classe **RoleEntryPoint** et toutes les tâches d’arrière-plan. Utiliser du code pour redémarrer les tâches qui échouent lorsque cela est approprié et lever l’exception pour redémarrer le rôle uniquement si vous ne pouvez normalement récupérer de la défaillance dans votre code.
  - Si vous utilisez des tâches de démarrage, vous êtes responsable de la gestion de l’exécution de la tâche et en cas d’échec de la vérification.
- Gestion et analyse des tâches de démarrage sont plus difficile que l’utilisation de l’approche de la classe **RoleEntryPoint** . Toutefois, le WebJobs d’Azure SDK inclut un tableau de bord pour le rendre plus facile à gérer que vous lancer dans des tâches de démarrage WebJobs.

### <a name="more-information"></a>Plus d’informations

- [Modèle de Consolidation des ressources de calcul](http://msdn.microsoft.com/library/dn589778.aspx)
- [Mise en route avec le Kit de développement logiciel WebJobs Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)

## <a name="azure-virtual-machines"></a>Machines virtuelles Azure

Tâches en arrière-plan peuvent être implémentées de façon qui les empêche d’être déployé vers Azure Web applications ou de Services en nuage, ou de ces options peut ne pas être pratiques. Exemples : services de Windows, des utilitaires tiers et programmes exécutables. Un autre exemple peut-être être des programmes écrits pour un environnement d’exécution qui est différent de celle qui héberge l’application. Par exemple, il peut être un programme Unix ou Linux que vous souhaitez exécuter à partir d’une application Windows ou .NET. Vous pouvez choisir parmi une gamme de systèmes d’exploitation pour une machine virtuelle Azure et exécuter le service ou l’exécutable sur cet ordinateur virtuel.

Pour vous aider à choisir d’utiliser des ordinateurs virtuels, consultez [Services d’application Azure, de Services en nuage et de comparaison des Machines virtuelles](./app-service-web/choose-web-site-cloud-service-vm.md). Pour plus d’informations sur les options pour les Machines virtuelles, consultez [formats de Machine virtuelle et le Service en nuage pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Pour plus d’informations sur les systèmes d’exploitation et les images prédéfinies qui sont disponibles pour les Machines virtuelles, consultez [Azure Marketplace de Machines virtuelles](https://azure.microsoft.com/gallery/virtual-machines/).

Pour lancer la tâche d’arrière-plan dans un ordinateur virtuel distinct, vous disposez d’une gamme d’options :

- Vous pouvez exécuter la tâche sur demande directement à partir de votre application en envoyant une demande à un point de terminaison qui expose de la tâche. Il transmet toutes les données qu’elle nécessite. Ce point de terminaison appelle la tâche.
- Vous pouvez configurer la tâche à exécuter selon une planification à l’aide d’un planificateur ou un timer qui est disponible dans votre système d’exploitation choisi. Par exemple, sous Windows, vous pouvez utiliser le Planificateur de tâches Windows pour exécuter des scripts et des tâches. Ou bien, si vous disposez de SQL Server installée sur l’ordinateur virtuel, vous pouvez utiliser l’Agent de SQL Server pour exécuter des scripts et des tâches.
- Vous pouvez utiliser le planificateur Azure pour lancer la tâche par l’ajout d’un message à une file d’attente de la tâche est à l’écoute sur, ou en envoyant une requête à une API qui expose de la tâche.

Reportez-vous à la rubrique [déclencheurs](#triggers) pour plus d’informations sur la façon dont vous pouvez lancer des tâches en arrière-plan.  

### <a name="considerations"></a>Considérations relatives à la

Tenez compte des points suivants lorsque vous décidez s’il faut déployer des tâches en arrière-plan sur une machine virtuelle Azure :

- Hébergement des tâches en arrière-plan dans une autre machine virtuelle Azure offre souplesse et permet un contrôle précis sur l’ouverture, l’exécution, la planification et la répartition des ressources. Néanmoins, il sera plus coûteuses à l’exécution si une machine virtuelle doit être déployée pour exécuter des tâches en arrière-plan.
- Il n’existe aucun outil permettant de surveiller les tâches dans le portail Azure et pas de fonction de redémarrage automatique pour les tâches qui ont échoué, bien que vous pouvez surveiller l’état de base de l’ordinateur virtuel et le gérer à l’aide de la [Gestion des applets de commande Service Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). Toutefois, il n’y a pas de fonction permettant de contrôler les processus et les threads dans les nœuds de calcul. En règle générale, à l’aide d’une machine virtuelle nécessite des efforts supplémentaires pour implémenter un mécanisme de collecte des données de l’instrumentation dans la tâche et du système d’exploitation de la machine virtuelle. Une solution qui peut s’avérer appropriée consiste à utiliser le [Management Pack System Center pour Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Vous pouvez envisager de créer des sondes de surveillance qui sont exposées par le biais de points de terminaison HTTP. Le code de ces sondes peut effectuer des vérifications de la santé, collecter des informations opérationnelles et statistiques--ou rassemble des informations sur l’erreur et renvoyer à une application de gestion. Pour plus d’informations, consultez [Modèle de surveillance de point de terminaison de santé](http://msdn.microsoft.com/library/dn589789.aspx).

### <a name="more-information"></a>Plus d’informations

- [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) sur Azure
- [Forum aux questions sur les Machines virtuelles Azure](virtual-machines/virtual-machines-linux-classic-faq.md)

## <a name="design-considerations"></a>Considérations de conception

Il y a plusieurs facteurs essentiels à prendre en compte lorsque vous concevez des tâches en arrière-plan. Les sections suivantes traitent de partitionnement, de conflits et de coordination.

## <a name="partitioning"></a>Partitionnement

Si vous décidez d’inclure les tâches en arrière-plan dans une instance existante de calcul (par exemple, une application web, rôle web, existants rôle de travail ou ordinateur virtuel), vous devez envisager comment elles affectent les attributs de qualité de l’instance de calcul et de la tâche d’arrière-plan. Ces facteurs vous aideront à décider s’il faut peut-être co-implanter les tâches avec l’instance de calcul existants ou de les séparer dans une instance de calcul distincts :

- **Disponibilité**: tâches en arrière-plan n’ayez pas à avoir le même niveau de disponibilité d’autres parties de l’application, en particulier, l’interface utilisateur et autres parties directement impliquées dans l’interaction de l’utilisateur. Tâches en arrière-plan peuvent être plus tolérants de latence, les échecs de nouvelle tentative de connexion et autres facteurs qu’affectent la disponibilité, car les opérations peuvent être mis en attente. Toutefois, il doit être suffisant pour empêcher la sauvegarde des demandes qui risque de bloquer les files d’attente et d’affecter l’application dans son ensemble.
- **Évolutivité**: tâches en arrière-plan sont susceptibles d’avoir une exigence d’évolutivité différents à l’interface utilisateur et les parties interactives de l’application. Mise à l’échelle de l’interface utilisateur peut-être être nécessaire pour répondre aux pics de la demande, alors que les tâches d’arrière-plan en attente peuvent être terminées au cours des périodes de faible activité par un moins nombre d’instances de calcul.
- **Résilience**: échec d’une instance de calcul qui héberge uniquement les tâches en arrière-plan peut affecter mortellement pas l’application dans son ensemble si les demandes de ces tâches peuvent être en attente ou ajournées jusqu'à ce que la tâche est à nouveau disponible. Si l’instance de calcul et/ou de tâches peuvent être redémarrés dans un intervalle approprié, les utilisateurs de l’application ne peuvent pas être affectés.
- **Sécurité**: tâches en arrière-plan peuvent avoir des exigences de sécurité différentes ou des restrictions à l’interface utilisateur ou d’autres parties de l’application. À l’aide d’une instance de calcul distincts, vous pouvez spécifier un environnement de sécurité différentes pour les tâches. Vous pouvez également utiliser des modèles de contrôle d’appels pour isoler les instances de calcul d’arrière-plan à partir de l’interface utilisateur afin d’optimiser la sécurité et la séparation.
- **Performances**: vous pouvez choisir le type de l’instance de calcul pour les tâches en arrière-plan à aligner plus précisément les exigences de performance des tâches. Cela peut signifier à l’aide d’une option de calcul moins coûteuse si les tâches ne nécessitent pas les mêmes capacités de traitement que l’interface utilisateur, ou une instance supérieure s’ils nécessitent des ressources et une capacité supplémentaire.
- **La facilité de gestion**: tâches en arrière-plan peuvent avoir un autre rythme de développement et de déploiement depuis le code de l’application principale ou de l’interface utilisateur. Les déployer dans une instance de calcul distinct peuvent simplifier les mises à jour et la gestion des versions.
- **Coût**: ajout de calculer des instances d’exécution augmente de tâches en arrière-plan les coûts d’hébergement. Vous devez réfléchir soigneusement le compromis entre des capacités supplémentaires et ces coûts supplémentaires.

Pour plus d’informations, consultez [Modèle élection de Leader](http://msdn.microsoft.com/library/dn568104.aspx) et [Modèle de consommateurs en concurrence](http://msdn.microsoft.com/library/dn568101.aspx).

## <a name="conflicts"></a>Conflits

Si vous avez plusieurs instances d’une tâche en arrière-plan, il est possible qu’ils entreront pour l’accès aux ressources et aux services, tels que les bases de données et de stockage. Cet accès simultané peut provoquer des conflits de ressources, ce qui peut provoquer des conflits dans la disponibilité des services et de l’intégrité des données dans le stockage. Vous pouvez résoudre les conflits de ressources à l’aide d’une approche de verrouillage pessimiste. Ainsi, en concurrence les instances d’une tâche à partir de l’accès à un service simultanément ou de corrompre des données.

Une autre approche pour résoudre les conflits consiste à définir des tâches en arrière-plan comme un singleton, afin qu’il y a jamais qu’une seule instance en cours d’exécution. Toutefois, ce qui élimine les avantages de fiabilité et de performances susceptibles de fournir une configuration de plusieurs instances. Cela est particulièrement vrai si l’interface utilisateur peut fournir la quantité de travail suffisante pour occuper plus d’une tâche d’arrière-plan.

Il est indispensable de s’assurer que la tâche en arrière-plan peut redémarrer automatiquement et qu’il possède une capacité suffisante pour faire face aux pics de la demande. Vous pouvez y parvenir en allouant une instance de calcul avec des ressources suffisantes, par l’implémentation d’un mécanisme de file d’attente qui peut stocker des demandes pour une exécution ultérieure lorsque la demande diminue, ou en utilisant une combinaison de ces techniques.

## <a name="coordination"></a>Coordination

Les tâches d’arrière-plan peuvent être complexes et peuvent nécessiter plusieurs tâches à exécuter pour produire un résultat ou à toutes les exigences. Il est courant dans les scénarios suivants pour diviser la tâche en plus petites étapes ou des tâches subordonnées qui peuvent être exécutées par plusieurs clients. Les travaux en plusieurs étapes peuvent être plus efficace et plus souple car les étapes individuelles peuvent être réutilisables dans plusieurs tâches. Il est également facile à ajouter, supprimer ou modifier l’ordre des étapes.

Coordination des tâches et des étapes multiples peut s’avérer difficile, mais il existe trois modèles courants que vous pouvez utiliser pour guider votre mise en œuvre d’une solution :

- **La décomposition d’une tâche en plusieurs étapes réutilisables**. Une application peut être nécessaire pour effectuer de nombreuses tâches de complexité variable sur les informations qu’il traite. Une approche simple mais stricte à l’implémentation de cette application peut être pour effectuer ce traitement comme un module monolithique. Toutefois, cette approche est susceptible de réduire les risques de refactorisation du code, optimisant ou réutiliser si des parties du même traitement sont requises ailleurs dans l’application. Pour plus d’informations, consultez [canaux et modèle de filtres](http://msdn.microsoft.com/library/dn568100.aspx).
- **Gestion de l’exécution des étapes d’une tâche**. Une application peut effectuer des tâches qui composent un nombre d’étapes (certains d'entre eux peuvent invoquer des services à distance ou accéder aux ressources distantes). Les étapes individuelles peuvent être indépendantes de l’autre, mais ils sont orchestrés par l’application qui implémente la tâche. Pour plus d’informations, consultez [Modèle de superviseur du Planificateur de l’Agent](http://msdn.microsoft.com/library/dn589780.aspx).
- **Gestion de la récupération pour les étapes de tâche qui échouent**. Une application peut avoir besoin annuler le travail qui est effectué par une série d’étapes (qui définissent ensemble une opération cohérente finalement) si une ou plusieurs des étapes échouent. Pour plus d’informations, consultez [Modèle de Transaction de compensation](http://msdn.microsoft.com/library/dn589804.aspx).

## <a name="lifecycle-cloud-services"></a>Cycle de vie (Services en nuage)

 Si vous décidez d’implémenter des travaux en arrière-plan pour les applications de Services en nuage qui utilisent des rôles web et worker à l’aide de la classe **RoleEntryPoint** , il est important de comprendre le cycle de vie de cette classe pour pouvoir le pour utiliser correctement.

Rôles Web et worker passent par des phases distinctes en démarrer, exécuter et arrêter. La classe **RoleEntryPoint** expose une série d’événements qui indiquent quand ces étapes sont produisent. Vous utilisez ces options pour initialiser, exécuter et arrêtez les tâches d’arrière-plan personnalisé. La durée du cycle est la suivante :

- Azure charge l’assembly du rôle et il recherche une classe qui dérive de **RoleEntryPoint**.
- S’il trouve cette classe, il appelle **RoleEntryPoint.OnStart()**. Vous substituez cette méthode pour initialiser vos tâches en arrière-plan.
- À l’issue de la méthode **OnStart** , appels Azure **Application_Start()** dans le fichier de l’application globale s’il est présent (par exemple, Global.asax dans un rôle web ASP.NET en cours d’exécution).
- Azure appelle **RoleEntryPoint.Run()** sur un nouveau thread de premier plan qui s’exécute en parallèle avec **OnStart()**. Vous substituez cette méthode pour démarrer les tâches d’arrière-plan.
- À la fin de la méthode Run, Azure appelle d’abord **Application_End()** dans le fichier de l’application globale si ce n’est présent, puis appelle **RoleEntryPoint.OnStop()**. Vous substituez la méthode **OnStop** pour arrêter vos tâches en arrière-plan, nettoyer les ressources, supprimer les objets et fermer des connexions qui les tâches peuvent avoir utilisé.
- Le processus hôte du rôle Collaborateur Azure est arrêté. À ce stade, le rôle sera recyclé et redémarrera.

Pour plus d’informations et un exemple de l’utilisation des méthodes de la classe **RoleEntryPoint** , consultez [Calculer un modèle de Consolidation de ressource](http://msdn.microsoft.com/library/dn589778.aspx).

## <a name="considerations"></a>Considérations relatives à la

Tenez compte des points suivants lorsque vous planifiez l’exécution des tâches d’arrière-plan dans un rôle web ou travailleur :

- L’implémentation de méthode **s’exécutent** par défaut dans la classe **RoleEntryPoint** contienne un appel de **thread.Sleep (Timeout.Infinite)** qui maintient le rôle actif indéfiniment. Si vous substituez la méthode **Run** (qui est généralement nécessaire pour exécuter des tâches en arrière-plan), vous ne devez pas autoriser votre code à quitter la méthode sauf si vous souhaitez recycler l’instance de rôle.
- Une implémentation classique de la méthode **Run** inclut le code pour démarrer chacun des tâches en arrière-plan et un élément de boucle qui vérifie périodiquement l’état de toutes les tâches d’arrière-plan. Il peut redémarrer tout échec ou surveiller les jetons d’annulation qui indiquent que les tâches terminées.
- Si une tâche d’arrière-plan lève une exception non gérée, cette tâche doit être recyclée tout en autorisant d’autres tâches d’arrière-plan dans le rôle de continuer à s’exécuter. Toutefois, si l’exception est provoquée par une corruption des objets en dehors de la tâche, notamment le stockage partagé, l’exception doit être gérée par votre classe de **RoleEntryPoint** , toutes les tâches doivent être annulées et la méthode **Run** doit être autorisée à la fin. Azure redémarre alors le rôle.
- Utilisez la méthode **OnStop** pour suspendre ou arrêter les tâches en arrière-plan et nettoyer les ressources. Cela peut impliquer l’arrêt des tâches de longue durée ou en plusieurs étapes. Il est essentiel d’envisager comment cela peut être fait pour éviter les incohérences de données. Si une instance de rôle s’arrête pour une raison quelconque autre qu’un arrêt initié par l’utilisateur, le code exécuté dans la méthode **OnStop** doit aboutir dans les cinq minutes avant d’être arrêté force. Vérifiez que votre code peut être effectué dans ce délai ou tolère ne pas en cours d’exécution jusqu'à la fin.  
- L’équilibreur de charge Azure commence à diriger le trafic vers l’instance de rôle lorsque la méthode **RoleEntryPoint.OnStart** renvoie la valeur **true**. Par conséquent, envisagez de placer tout votre code d’initialisation dans la méthode **OnStart** de sorte que des instances de rôle qui ne s’initialisent pas correctement ne recevra pas tout le trafic.
- Vous pouvez utiliser les tâches de démarrage en plus des méthodes de la classe **RoleEntryPoint** . Vous devez utiliser des tâches de démarrage pour initialiser tous les paramètres que vous modifiez dans l’équilibreur de charge Azure, car ces tâches s’exécutent avant le rôle reçoit toutes les demandes. Pour plus d’informations, consultez [exécution de tâches de démarrage dans Azure](./cloud-services/cloud-services-startup-tasks.md).
- S’il y a une erreur dans une tâche de démarrage, il peut forcer le rôle de redémarrer continuellement. Cela peut vous empêcher d’effectuer un échange d’adresse IP (VIP) virtuel à une version précédemment mis en place, car l’échange requiert un accès exclusif au rôle. Cela ne peut être obtenu pendant le redémarrage du rôle. Pour résoudre ce problème :
    -  Ajoutez le code suivant au début des méthodes **OnStart** et **exécutez** dans votre rôle de :

    ```C#
    var freeze = CloudConfigurationManager.GetSetting("Freeze");
    if (freeze != null)
    {
        if (Boolean.Parse(freeze))
        {
            Thread.Sleep(System.Threading.Timeout.Infinite);
        }
    }
    ```

   - Ajoutez la définition du paramètre **Figer** sous la forme d’une valeur de type Boolean à le ServiceDefinition.csdef et le ServiceConfiguration. *fichiers .cscfg pour le rôle et la valeur* *false* *. Si le rôle est en mode de redémarrage répété, vous pouvez modifier le paramètre pour * *true** pour figer l’exécution du rôle et de lui permettre d’être remplacée par une version précédente.

## <a name="resiliency-considerations"></a>Considérations relatives à la résistance

Tâches en arrière-plan doivent être résilientes pour fournir des services fiables à l’application. Lorsque vous planifiez et créez des tâches en arrière-plan, tenez compte des points suivants :

- Tâches en arrière-plan doivent être en mesure de gérer correctement les redémarrages rôle ou service sans endommager les données ou introduire des incohérences dans l’application. Pour les tâches de longue durée ou en plusieurs étapes, envisagez d’utiliser la _vérification du pointage_ en enregistrant l’état des travaux dans un stockage persistant, ou sous forme de messages dans une file d’attente si cela est approprié. Par exemple, vous pouvez conserver les informations d’état dans un message dans une file d’attente et la mise à jour de façon incrémentielle ces informations d’état avec la progression de la tâche afin que la tâche peut être traitée à partir du dernier connu bon point de contrôle, au lieu de redémarrer à partir du début. Lorsque vous utilisez des files d’attente du Bus des services Azure, vous pouvez utiliser des sessions de messagerie pour activer le même scénario. Sessions vous permettent d’enregistrer et de récupérer l’état de traitement de l’application en utilisant les méthodes [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) et [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) . Pour plus d’informations sur la conception des workflows et des processus en plusieurs étapes fiables, voir [Modèle de superviseur du Planificateur de l’Agent](http://msdn.microsoft.com/library/dn589780.aspx).
- Lorsque vous utilisez des rôles web ou travailleur pour héberger plusieurs tâches d’arrière-plan, concevez votre substitution de la méthode **Run** à surveiller pour les tâches bloquées ou ayant échouées et de les redémarrer. Dans le cas où cela n’est pas pratique, et que vous utilisez un rôle worker, forcer le rôle de travail redémarrer en sortie de la méthode **Run** .
- Lorsque vous utilisez des files d’attente à communiquer avec des tâches en arrière-plan, les files d’attente peuvent agir comme une mémoire tampon pour stocker les demandes qui sont envoyées aux tâches lors de l’application est sous plus élevé que la charge habituelle. Ainsi, les tâches afin de prendre en compte l’interface utilisateur pendant les périodes creuses. Cela signifie également que le rôle de recyclage ne bloque pas l’interface utilisateur. Pour plus d’informations, consultez [modèle de l’audit de charge basé sur la file d’attente](http://msdn.microsoft.com/library/dn589783.aspx). Si certaines tâches sont plus importants que d’autres, envisagez d’implémenter le [Modèle de file d’attente de priorité](http://msdn.microsoft.com/library/dn589794.aspx) pour garantir l’exécutent de ces tâches avant celles moins importantes.
- Tâches en arrière-plan qui sont établies par les messages ou traiter les messages doivent être conçus pour gérer des incohérences, par exemple les messages qui arrivent en désordre, messages de manière répétée, provoquent une erreur (souvent appelée _messages poison_) et qui sont livrés en plusieurs fois. Considérez les points suivants :
  - Messages qui doivent être traités dans un ordre spécifique, telles que celles qui modifient les données en fonction de la valeur de données existante (par exemple, l’ajout d’une valeur à une valeur existante), peut ne pas arriver dans l’ordre dans lequel ils ont été envoyés. Également, ils peuvent être gérés par les différentes instances d’une tâche d’arrière-plan dans un ordre différent en raison de diverses charges sur chaque instance. Les messages qui doivent être traités dans un ordre spécifique doivent inclure un numéro de séquence, de clé ou d’un autre indicateur de tâches en arrière-plan permet de s’assurer qu’ils sont traités dans l’ordre correct. Si vous utilisez le Bus des services Azure, vous pouvez utiliser des sessions de messagerie afin de garantir l’ordre de livraison. Toutefois, il est généralement plus efficace, si possible concevoir le processus afin que l’ordre des messages n’est pas important.
  - En règle générale, une tâche d’arrière-plan va lire les messages dans la file d’attente, qui masque temporairement les autres consommateurs de message. Il supprime ensuite les messages après que qu’ils ont été traités. Si une tâche d’arrière-plan échoue lors du traitement d’un message, ce message réapparaîtra dans la file d’attente après l’expiration de lecture. Il sera traité par une autre instance de la tâche ou au cours du prochain cycle de traitement de cette instance. Si le message génère toujours une erreur dans le consommateur, il bloquera la tâche de la file d’attente et finit par l’application elle-même lorsque la file d’attente est pleine. Par conséquent, il est essentiel pour détecter et supprimer les messages incohérents à partir de la file d’attente. Si vous utilisez le Bus des services Azure, les messages qui provoquent une erreur peuvent être déplacés automatiquement ou manuellement à une file d’attente des lettres mortes associé.
  - Files d’attente sont garantis au _moins une fois_ les mécanismes de remise, mais peut fournir plusieurs fois le même message. En outre, si une tâche d’arrière-plan échoue après le traitement d’un message, mais avant de le supprimer de la file d’attente, le message devient disponible pour le traitement de nouveau. Tâches en arrière-plan doivent être idempotents, ce qui signifie que plusieurs fois le même message de traitement ne provoque pas d’erreur ou d’une incohérence dans les données de l’application. Certaines opérations sont naturellement idempotent, telles que la définition d’une valeur stockée à une valeur spécifique de nouveau. Toutefois, les opérations telles que l’ajout d’une valeur à une valeur stockée sans vérifier que la valeur stockée est toujours la même que lorsque le message a été envoyé provoquerait des incohérences. Files d’attente de Bus des services Azure peuvent être configurés pour supprimer automatiquement des messages en double.
  - Certains systèmes de messagerie, tels que les files d’attente de stockage Azure et files d’attente du Bus des services Azure, prend en charge une propriété count de-queue qui indique le nombre de fois qu’un message a été lu à partir de la file d’attente. Cela peut être utile dans le traitement des messages répétés et incohérents. Pour plus d’informations, consultez [Introduction à la messagerie asynchrone](http://msdn.microsoft.com/library/dn589781.aspx) et les [Motifs de l’idempotence](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## <a name="scaling-and-performance-considerations"></a>Considérations de performances et de la mise à l’échelle

Tâches en arrière-plan doivent offrir des performances suffisantes pour s’assurer qu’ils ne pas bloquer l’application ou de provoquer des incohérences en raison d’une opération différée lorsque le système est sous charge. En règle générale, les performances sont améliorées par les instances de calcul qui hébergent les tâches en arrière-plan de mise à l’échelle. Lorsque vous planifiez et créez des tâches en arrière-plan, tenez compte des points suivants autour de l’évolutivité et les performances :

- Prend en charge Azure autoscaling (mise à l’échelle et mise à l’échelle en) en fonction de la demande actuelle et de la charge, ou selon une planification prédéfinie, d’applications Web, les web Services en nuage et de rôles worker et de Machines virtuelles hébergées déploiements. Cette fonctionnalité permet de garantir que l’application dans son ensemble dispose de fonctionnalités de performances suffisantes tout en minimisant les coûts de l’exécution.
- Lorsqu’une capacité de performance à partir des autres parties d’une application de Services en nuage (par exemple, l’interface utilisateur ou des composants tels que la couche d’accès aux données) ont des tâches en arrière-plan, hébergeant les tâches d’arrière-plan dans un rôle de travail séparé permet de l’interface utilisateur et l’arrière-plan des rôles de tâche à mettre à l’échelle indépendamment pour gérer la charge. Si plusieurs tâches d’arrière-plan possèdent des capacités de performances considérablement différent des autres, pensez à leur division en rôles de travail séparé et la mise à l’échelle indépendamment de chaque type de rôle. Notez toutefois que ceci peut augmenter les coûts du runtime par rapport à la combinaison de toutes les tâches en moins de rôles.
- Tout simplement en redimensionnant les rôles n’est peut-être pas suffisante pour éviter la perte de performances sous charge. Vous devrez peut-être également mettre à l’échelle des files d’attente de stockage et d’autres ressources pour empêcher un point unique générale de la chaîne de devenir un goulot d’étranglement de traitement. Envisagez également les autres limitations, comme le débit maximal de stockage et d’autres services nécessitant l’application et les tâches d’arrière-plan.
- Tâches en arrière-plan doivent être conçus pour la mise à l’échelle. Par exemple, qu’ils doivent être en mesure de détecter de manière dynamique le nombre de files d’attente de stockage en cours d’utilisation pour écouter ou envoyer des messages à la file d’attente appropriée.
- Par défaut, l’échelle WebJobs avec leur instance Azure Web Apps associée. Toutefois, si vous souhaitez un WebJob pour l’exécuter en tant qu’une seule instance, vous pouvez créer un fichier Settings.job qui contient les données JSON **{« is_singleton » : true}**. Cela force Azure ne s’exécute qu’une seule instance de le WebJob, même s’il existe plusieurs instances de l’application web associée. Cela peut être une technique utile pour les tâches planifiées qui doivent s’exécuter sous la forme d’une seule instance.

## <a name="related-patterns"></a>Modèles connexes

- [AMORCES de messagerie asynchrone](http://msdn.microsoft.com/library/dn589781.aspx)
- [AutoScaling conseils](http://msdn.microsoft.com/library/dn589774.aspx)
- [Modèle de Transaction de compensation](http://msdn.microsoft.com/library/dn589804.aspx)
- [Modèle de consommateurs concurrentes](http://msdn.microsoft.com/library/dn568101.aspx)
- [Calculer le partitionnement des conseils](http://msdn.microsoft.com/library/dn589773.aspx)
- [Modèle de Consolidation des ressources de calcul](http://msdn.microsoft.com/library/dn589778.aspx)
- [Modèle de contrôle d’appels](http://msdn.microsoft.com/library/dn589793.aspx)
- [Modèle élection de leader](http://msdn.microsoft.com/library/dn568104.aspx)
- [Canaux de communication et modèle de filtres](http://msdn.microsoft.com/library/dn568100.aspx)
- [Modèle de file d’attente de priorité](http://msdn.microsoft.com/library/dn589794.aspx)
- [L’audit modèle de charge basé sur la file d’attente](http://msdn.microsoft.com/library/dn589783.aspx)
- [Modèle de superviseur de l’Agent du planificateur](http://msdn.microsoft.com/library/dn589780.aspx)

## <a name="more-information"></a>Plus d’informations

- [Mise à l’échelle des Applications Azure avec rôles Worker](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [L’exécution des tâches en arrière-plan](http://msdn.microsoft.com/library/ff803365.aspx)
- [Cycle de vie de démarrage rôle Azure](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (publication de blog)
- [Cycle de vie rôle Azure Cloud Services](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vidéo)
- [Mise en route avec le Kit de développement WebJobs Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- [Azure files d’attente et Service Bus - comparées et comparaison](./service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Comment faire pour activer les Diagnostics dans un Service Cloud](./cloud-services/cloud-services-dotnet-diagnostics.md)
