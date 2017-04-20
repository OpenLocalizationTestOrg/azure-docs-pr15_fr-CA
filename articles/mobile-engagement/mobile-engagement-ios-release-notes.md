<properties
    pageTitle="E/s Mobile Engagement Azure SDK Release Notes | Microsoft Azure"
    description="Dernières mises à jour et les procédures pour iOS SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="piyushjo" />

#<a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Notes de version iOS de Mobile Engagement Azure SDK

##<a name="400-09122016"></a>4.0.0 (12/09/2016)

-   Notification fixe non traitée sur les périphériques d’e/s 10.
-   Déconseiller XCode 7.

##<a name="324-06302016"></a>3.2.4 (30/06/2016)

-   Fixe d’agrégation entre les journaux techniques et autres.

##<a name="323-06072016"></a>3.2.3 (07/06/2016)

-   Corrigé le bogue dans lequel les commentaires de livraison ne sont pas signalé lorsque l’application est en arrière-plan.
-   Optimiser l’envoi des journaux techniques.

##<a name="322-04072016"></a>3.2.2 (07/04/2016)

-   Bogue sur annulation de demande HTTP qui entraîne parfois des incidents.

##<a name="321-12112015"></a>3.2.1 (11/12/2015)

-   Fixe le délai lorsqu’une nouvelle instance de l’application est déclenchée par une notification avec les liens de profondes

##<a name="320-10082015"></a>3.2.0 (08/10/2015)

-   Activé Bitcode dans le Kit de développement logiciel pour qu’il fonctionne avec **Xcode 7**.
-   Bogues résolus relatifs à des notifications dans l’application.
-   Faites les notifications dans l’application plus fiable en cas de niveau de batterie faible et d’autres cas de figure.
-   Permet de supprimer des journaux de console supplémentaires générés par la bibliothèque de 3e partie.

##<a name="310-08262015"></a>3.1.0 (26/08/2015)

-   Corriger des bogues de compatibilité iOS 9 avec une bibliothèque tierce. Il était à l’origine des blocages lors de l’envoi interroge les résultats, des informations sur l’application ou des données supplémentaires.

##<a name="300-06192015"></a>3.0.0 (19/06/2015)

-   Engagement de Mobile utilise des Notifications Push en mode silencieux.
-   Supprimer la prise en charge des e/s 4.X. À partir de cette version de la cible de déploiement de votre application doit être d’au moins 6 d’iOS.

##<a name="220-05212015"></a>2.2.0 (21/05/2015)

-   L’id de périphérique pour les périphériques mobiles Engagement < iOS 6 est désormais basée sur un GUID généré au moment de l’installation.

##<a name="210-04242015"></a>2.1.0 (24/04/2015)

-   Grande compatibilité Swift.
-   Lorsque vous cliquez sur une notification, l’action de QU'URL est désormais exécutée droite après l’ouverture de l’application.
-   Ajout fichier d’en-tête manquant dans le package du Kit de développement logiciel.
-   Résolution d’un problème lors de la reporter sur incident Mobile Engagement a été désactivé.

##<a name="200-02172015"></a>2.0.0 (17/02/2015)

-   Version initiale de l’Engagement Mobile Azure
-   configuration de l’appId/sdkKey est remplacée par une configuration de chaîne de connexion.
-   Supprimer API pour envoyer et recevoir des messages XMPP arbitraires à partir des entités XMPP arbitraires.
-   Supprimer API pour envoyer et recevoir des messages entre les périphériques.
-   Améliorations de la sécurité.
-   Suivi de SmartAd est supprimé.
