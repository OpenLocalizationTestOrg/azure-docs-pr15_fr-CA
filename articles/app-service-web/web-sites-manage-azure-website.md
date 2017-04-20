<properties 
    pageTitle="Gérer une application web dans le Service d’application Azure" 
    description="Liens vers des ressources pour la gestion d’une application web dans le Service d’application Azure." 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="rachelap"/>

# <a name="manage-a-web-app-in-azure-app-service"></a>Gérer une application web dans le Service d’application Azure

Cette rubrique contient des liens vers les ressources pour la gestion d’une application web dans le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714). La gestion inclut toutes les tâches qui empêchent votre application web fonctionne correctement. 

La durée de vie d’une application web, vous allez effectuer les différentes tâches de gestion, lorsque vous vous déplacez de déploiement initial pour les mises à jour, de maintenance et de fonctionnement normal.

De nombreuses tâches de gestion d’application web peuvent être effectuées dans le portail Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Avant de déployer votre application web à la production

### <a name="choose-a-tier"></a>Choisissez une couche

Service d’application Azure est proposée en cinq niveaux : libre, Shared, Basic, Standard et Premium. Pour plus d’informations sur les fonctionnalités et le prix de chaque niveau, reportez-vous à la section [informations de tarification](/pricing/details/app-service/). 

- [Plans de Service d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) vous permettent de regrouper plusieurs applications web sous le même niveau.
- Vous pouvez toujours [Basculer les niveaux](web-sites-scale.md) après avoir créé votre application web.

### <a name="configuration"></a>Configuration de

Le [Portail Azure](https://portal.azure.com/) permet de définir diverses options de configuration. Pour plus d’informations, consultez [Configuration des applications web dans le Service d’application Azure](web-sites-configure.md). Voici une liste rapide :

- Sélectionnez les **versions du runtime** de .NET, PHP, Java ou Python, si nécessaire.
- Activer le **protocole WebSocket** si votre application web utilise le protocole WebSocket. (Cela inclut les applications qui utilisent [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](web-sites-nodejs-chat-app-socketio.md)).
- Vous exécutez les travaux web continue ? Si tel est le cas, activez **Toujours sur**.
- Définir le **document par défaut**, tel que index.html.

Outre ces paramètres de configuration de base, vous souhaiterez peut-être configurer les éléments suivants :

- Cryptage **Secure Socket Layer (SSL)** . Pour utiliser SSL avec un nom de domaine personnalisé, vous devez obtenir un certificat SSL et configurer votre application web afin de l’utiliser. Reportez-vous à la section [Activer le protocole HTTPS pour une application web dans le Service d’application Azure](web-sites-configure-ssl-certificate.md).
- **Nom de domaine personnalisé.** Votre application web a automatiquement un sous-domaine sous azurewebsites.net. Vous pouvez associer un nom de domaine personnalisé, par exemple, contoso.com. Voir [configuration d’un nom de domaine personnalisé dans le Service d’application Azure](web-sites-custom-domain-name.md).

Configuration de la langue spécifique :

- **PHP**: [configurer PHP dans Azure Application Service Web Apps](web-sites-php-configure.md).
- **Python**: [configuration les Python avec Azure Application Service Web Apps](web-sites-python-configure.md)


## <a name="while-your-web-app-is-running"></a>Pendant l’exécution de votre application web

Pendant l’exécution de votre application web, vous souhaitez vous assurer qu’il est disponible et qu’il met à l’échelle pour satisfaire le trafic des utilisateurs. Vous devez également résoudre les erreurs.

### <a name="monitoring"></a>Surveillance

- Via le portail d’Azure, vous pouvez [Ajouter des mesures de performances](web-sites-monitor.md) telles que l’utilisation de l’UC et le nombre de demandes client.
- [L’échelle votre application web](web-sites-scale.md) en réponse au trafic. En fonction de votre niveau, vous pouvez accroître le nombre d’ordinateurs virtuels et/ou de la taille des instances de la machine virtuelle. Dans les couches Standard et Premium, vous pouvez également définir autoscaling, afin que votre application web s’adapte automatiquement, selon un calendrier fixe, soit en réponse à charger.  
 
### <a name="backups"></a>Sauvegardes

- Définir des [sauvegardes automatiques](web-sites-backup.md) de votre application web. Pour en savoir plus sur les sauvegardes dans [cette vidéo](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Obtenir des informations sur les options de [récupération de la base de données](../sql-database/sql-database-business-continuity.md) dans la base de données de SQL Azure.

### <a name="troubleshooting"></a>Résolution des problèmes

- Si quelque chose se passe mal, vous pouvez [résoudre les problèmes dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), à l’aide de journaux de diagnostic et de débogage en direct dans le nuage. 
- En dehors de Visual Studio, il existe différentes façons de collecter les journaux de diagnostic. Reportez-vous à la section [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure](web-sites-enable-diagnostic-log.md).
- Pour les applications de Node.js, consultez [comment déboguer une application web de Node.js dans le Service d’application Azure](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Restauration des données

- [Restaurer](web-sites-restore.md) une application web qui a été précédemment sauvegardée.


## <a name="when-you-update-your-web-app"></a>Lorsque vous mettez à jour votre application web

Si vous n’avez pas activé les sauvegardes automatiques, vous pouvez créer une [sauvegarde manuelle](web-sites-backup.md).

Envisagez d’utiliser le [déploiement par étapes](web-sites-staged-publishing.md). Cette option vous permet de publier des mises à jour à un déploiement de test qui s’exécute côte à côte avec votre déploiement en production. 

Si vous utilisez Visual Studio Team Services, vous pouvez configurer le déploiement en continu à partir du contrôle de code source :

- [À l’aide du contrôle de Version Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [À l’aide de Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  
