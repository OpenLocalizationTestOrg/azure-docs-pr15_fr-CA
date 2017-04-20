<properties
    pageTitle="Utiliser les Services mobiles, quel est l’application de Service ?"
    description="Découvrez quels sont les avantages App Service apporte à vos projets de Services mobiles."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>Utiliser les Services mobiles, quel est l’application de Service ?

##<a name="overview"></a>Vue d’ensemble
Votre Service Mobile existante est sûre et reste pris en charge. Cependant, il existe différents avantages par le *Service d’application Azure* platform fournit pour votre application mobile qui ne sont pas disponibles aujourd'hui les Services mobiles :

- Offre plus simple, plus facile et plus économique pour les applications qui incluent à la fois les clients web et mobiles
- Surveillance des nouvelles fonctionnalités de l’hôte, y compris les tâches Web, enregistrements CNAME personnalisé, une meilleure
- Intégration clé en main avec le Gestionnaire de trafic
- Connectivité aux VPN à l’aide de VNet en plus les connexions hybride et les ressources sur site
- Surveillance, d’alerte et le dépannage de votre application à l’aide de NewRelic ou AppInsights
- Spectre plus riche de l’actif sous-jacent calculer les ressources et les prix
- Échelle automatique intégrée, l’équilibrage de charge et l’analyse des performances.
- Intégré à la zone de transit, sauvegarde, retour arrière et de test de production

## <a name="new-hosting-features"></a>Nouvelles fonctionnalités d’hébergement
Dans le *Service d’application Azure* *l’Application Mobile* code principal s’exécute dans le même conteneur que l’application Web et d’application de l’API. En tant que tel, vous pouvez tirer parti de toutes les fonctionnalités dans ce conteneur, y compris ceux qui ne sont pas présents dans les Services mobiles :

- Ajouter une logique de back-end en permanence en cours d’exécution via les tâches Web
- Assurez-vous que votre code principal est toujours en cours d’exécution.
- Utiliser des enregistrements CNAME personnalisés pour fournir des noms conviviaux et stables à vos points de terminaison de serveur principal mobile
- Geo-échelle votre application avec le Gestionnaire de trafic
- Inclure les bibliothèques et les packages que vous souhaitez.
- (Pour .NET) Exploiter toutes les fonctionnalités de ASP.NET, y compris de MVC
- (Pour Node.js) Exploitez les bibliothèques JavaScript pur de l’écosystème du nœud, y compris les bibliothèques communes de MVC.

##<a name="access-on-premises-data-using-vnet"></a>Accès aux données à l’aide de VNet sur site
Avec les Services de Mobile aujourd'hui vous pouvez déjà utiliser hybride connexions pour accéder à des ressources sur site. Toutefois, il existe des situations où une solution VPN est préférable. Avec le *Service d’application Azure* , vous pouvez utiliser Azure VNet pour le code de votre application Mobile back-end.

##<a name="use-your-favorite-backend-language"></a>Utilisez votre langue préférée de back-end
*Service d’application Azure* offre plus large et plus riche prise en charge ASP.NET et Node.js plates-formes, y compris l’accès aux dernières runtimes.

##<a name="set-up-automatic-scale"></a>Configuration automatique de l’échelle
Avec les Services mobiles, toutes les instances de votre code principal ont été en cours d’exécution sur les ordinateurs virtuels de petite taille. *Azure Application Service* vous permet de sélectionner la taille des ordinateurs virtuels à partir d’un ensemble beaucoup plus riche d’options. Vous pouvez aussi rapidement mettre à l’échelle d’entrée ou de sortie pour gérer tout entrant de la charge du client, en fonction des différentes mesures de performances.

##<a name="be-in-the-know"></a>Dans le « connaître »
Réagir aux problèmes en temps réel avec la surveillance et les alertes pour vous informer automatiquement de vous et votre équipe. Intégrer analytique d’application avancée et des fonctionnalités de surveillance de Relic nouvelle et AppInsights pour obtenir des informations encore plus riche dans les performances de votre application Mobile. Avec le *Service d’application Azure* , vous pouvez maintenant configurer alertes en fonction des diverses mesures de performances, soit par programmation et via le portail Azure.

##<a name="keep-your-assets-safe"></a>Protégez vos ressources
Sauvegarder automatiquement votre serveur principal et la base de données. Votre code et les données sont protégées contre un sinistre et restaurer facilement, ce qui vous permet de gérer votre entreprise en toute confiance.

##<a name="ready-stage-go"></a>Prêts à l’emploi, étape, Go !
Avec le *Service d’application Azure* , vous pouvez maintenant créer des tests privés plusieurs environnements et reclassement pour vos applications mobiles. Elles permettent d’effectuer des tests avant de déployer. Remplacez à la production sans interruption de service. Les applications Web sont déjà chargées, assurer la meilleure expérience client.

Vous pouvez obtenir Profitez du *Service d’application* pour le Service Mobile existant en suivant ce [didacticiel](app-service-mobile-migrating-from-mobile-services.md).

