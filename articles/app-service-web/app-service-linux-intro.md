<properties 
    pageTitle="Introduction au Service d’application sous Linux | Microsoft Azure" 
    description="Obtenir des informations sur le Service d’application sous Linux." 
    keywords="service d’application Azure, linux, systèmes d’exploitation"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="introduction-to-app-service-on-linux"></a>Introduction au Service d’application sous Linux
Service d’application sous Linux est en cours d’évaluation et prend en charge les applications web en cours d’exécution en mode natif et sous Linux. 

## <a name="overview"></a>Vue d’ensemble ##
Clients peuvent utiliser Application Service sous Linux pour les applications web hôte en mode natif sous Linux pour piles d’applications pris en charge. La section fonctionnalités suivante répertorie les piles d’applications actuellement prises en charge.

## <a name="features"></a>Fonctionnalités ##
Service d’application sous Linux prend actuellement en charge les piles d’applications suivants

- Node.js
- PHP

Les clients peuvent déployer leurs applications à l’aide de

- FTP.
- Git local.
- GitHub ou BitBucket.

Pour la mise à l’échelle de l’application


- Clients peuvent faire évoluer leurs applications web haut et le bas en modifiant le niveau de leur Plan de Service d’application. 
- Les clients peuvent faire évoluer leurs applications et exécuter leur application sur plusieurs instances dans les limites de leur point de stock.

Pour Kudu les fonctionnalités de base fonctionnent

- Environnement.
- Déploiements.
- Console Visual Basic.

## <a name="limitations"></a>Limitations ##

Le portail de gestion Azure affiche les fonctionnalités actuellement prises en charge pour le Service d’application sous Linux uniquement et masquer les autres. En tant que notre équipe l’activation des fonctionnalités plus nous va conserver reflétant cette sur le portail de gestion. Certaines fonctionnalités comme l’intégration de VNET et DAS / d’authentification tiers ou extensions de site Kudu ne fonctionnent pas actuellement. Mais à mesure que nous ces utilisation nous mettons à jour notre blog sur les modifications et documentation.

Cette version d’évaluation n’est actuellement disponible dans les régions suivantes

-   États-Unis Ouest.
-   Europe de l’ouest.
-   Asie du Sud-est.

Application Web sous Linux est uniquement pris en charge dans dédié de Plans de Service application et ne dispose pas d’une couche libre ou partagé. Également, les plans de service d’application pour normal et des applications web de Linux sont mutuellement exclusives, vous ne pouvez pas créer une application web de Linux dans un plan de service d’application non-Linux.

Application Web sous Linux doit être créée dans un groupe de ressources qui ne contient-elle pas d’applications web de non-Linux dans la même région.

En raison du manque de recyclage avec chevauchement des applications web, les clients doivent s’attendre qu'un temps d’arrêt petit dans le cas d’une application web a été redémarré. 

## <a name="next-steps"></a>Étapes suivantes ##

Suivez les liens ci-dessous pour démarrer avec le Service d’application sous Linux. Veuillez publier des questions et remarques sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Création d’applications Web dans le Service d’application sous Linux](./app-service-linux-how-to-create-a-web-app.md)
* [À l’aide de la Configuration de PM2 pour Node.js dans les applications Web sous Linux](./app-service-linux-using-nodejs-pm2.md)

