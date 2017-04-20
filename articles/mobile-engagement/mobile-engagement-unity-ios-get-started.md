<properties
    pageTitle="Mise en route avec Azure Mobile Engagement pour le déploiement d’e/s de l’unité"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et d’envoyer des Notifications pour les applications Unity déployer sur des périphériques d’e/s."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Mise en route avec Azure Mobile Engagement pour le déploiement d’e/s de l’unité

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous indique comment utiliser Azure Mobile Engagement de comprendre l’utilisation de votre application et comment envoyer les notifications de type pousser à segmenté les utilisateurs d’une application Unity lors du déploiement sur un périphérique iOS.
Ce didacticiel utilise le rouleau Unity classique un didacticiel de ROTULE comme point de départ. Vous devez suivre les étapes de ce [didacticiel](mobile-engagement-unity-roll-a-ball.md) , avant de procéder à l’intégration de l’Engagement de Mobile que nous exposer dans le didacticiel ci-dessous. 

Ce didacticiel requiert les éléments suivants :

+ [Éditeur d’unité](http://unity3d.com/get-unity)
+ [Unité d’Engagement Mobile SDK](https://aka.ms/azmeunitysdk)
+ Éditeur de XCode

> [AZURE.NOTE] Pour terminer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’essai gratuit dans quelques minutes. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).

##<a id="setup-azme"></a>Engagement de Mobile le programme d’installation pour votre application iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application à la principale mission de Mobile

###<a name="import-the-unity-package"></a>Importer le package de l’unité

1. Télécharger le [package de l’unité Mobile Engagement](https://aka.ms/azmeunitysdk) et enregistrez-le sur votre ordinateur local. 

2. Accédez à **actifs -> Importer le Package -> Custom Package** et sélectionnez le package que vous avez téléchargé à l’étape ci-dessus. 

    ![][70] 

3. Assurez-vous que tous les fichiers sélectionnés et cliquez sur le bouton **Importer** . 

    ![][71] 

4. Une fois que l’importation est réussie, vous verrez les fichiers SDK importés dans votre projet.  

    ![][72] 

###<a name="update-the-engagementconfiguration"></a>Mise à jour de la EngagementConfiguration

1. Ouvrez le fichier de script **EngagementConfiguration** à partir du dossier du Kit de développement logiciel et de la mise à jour de le **e/s\_connexion\_chaîne** avec la chaîne de connexion que vous avez obtenu précédemment à partir du portail Azure.  

    ![][73]

2. Enregistrez le fichier. 

###<a name="configure-the-app-for-basic-tracking"></a>Configurer l’application de suivi de base

1. Ouvrez le script **PlayerController** associé à l’objet de lecteur pour le modifier. 

2. Ajoutez l’instruction using :

        using Microsoft.Azure.Engagement.Unity;

3. Ajoutez le code suivant à la `Start()` méthode
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

1. Se connecter à un périphérique d’e/s à votre ordinateur. 

2. Ouvrir **fichier -> Paramètres de génération** 

    ![][40]

3. Sélectionnez les **e/s** , puis cliquez sur la **Plate-forme de Switch**

    ![][41]

    ![][42]

4. Cliquez sur **paramètres du lecteur** et fournir un identificateur de groupe valide. 

    ![][53]

5. Cliquez sur **Générer et exécuter**

    ![][54]

6. Vous pouvez être invité à fournir un nom de dossier pour stocker le package d’e/s. 

    ![][43]

7. Si tout se passe bien, le projet est compilé, puis vous devez ouvrir sur votre application XCode. 

8. Assurez-vous que l' **identificateur d’offres groupées** est correct dans le projet.  

    ![][75]

10. Maintenant exécuter l’application dans XCode afin que le package est déployé sur le périphérique connecté et vous devez voir votre jeu Unity sur votre téléphone ! 

##<a id="monitor"></a>Application de se connecter grâce à une surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications de transmission et de la messagerie dans l’application

Engagement de Mobile vous permet d’interagir avec les utilisateurs et atteindre avec les notifications de transmission et de la messagerie dans le cadre de campagnes dans l’application. Ce module est appelé la portée dans le portail Mobile Engagement.
Vous n’êtes pas obligé d’effectuer une configuration supplémentaire dans votre application de recevoir des notifications et elle est déjà configurée pour elle.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
