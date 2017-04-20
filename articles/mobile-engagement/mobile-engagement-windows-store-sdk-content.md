<properties 
    pageTitle="Contenu du Kit de développement SDK Apps universel" 
    description="En savoir plus sur le contenu du Kit de développement Windows universel Apps pour Azure Mobile Engagement"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-content"></a>Contenu du Kit de développement SDK Apps universel

Ce document répertorie et décrit le contenu déployé par le Kit de développement de votre application.

##<a name="the-resources-folder"></a>Le `/Resources` dossier

Ce dossier contient toutes les ressources nécessitant un Engagement de Mobile. Vous pouvez également personnaliser les adapter à votre application.

- `EngagementConfiguration.xml`: Fichier de configuration de l’Engagement Mobile, voici où vous pouvez personnaliser les paramètres d’Engagement de Mobile (chaîne de connexion de Mobile Engagement, blocage du rapport...).

### <a name="html-folder"></a>dossier de /HTML

- `EngagementNotification.html`: Le `Notification` conception html d’affichage pour les bannières dans l’application web.

- `EngagementAnnouncement.html`: Le `Announcement` conception html d’affichage pour les affichages de spots dans l’application web.

### <a name="images-folder"></a>dossier de /images

- `EngagementIconNotification.png`: L’icône de la marque s’affiche à gauche d’une notification, remplacez celle-ci par l’icône de votre image de marque.

- `EngagementIconOk.png`: Le `Ok` l’icône des portée des pages de contenu pour le bouton d’action ou de la validation.

- `EngagementIconNOK.png`: Le `NOK` icône utilisée lorsque le bouton de validation des portée des pages de contenu est désactivé.
 
- `EngagementIconClose.png`: Le `Close` icône des notifications de la portée et le contenu pour le bouton faire disparaître.

### <a name="overlay-folder"></a>dossier de /Overlay

- `EngagementPageOverlay.cs`: La page de superposition de responsables de l’ajout de l’Engagement d’atteindre l’interface utilisateur dans l’application à son enfant.
  
