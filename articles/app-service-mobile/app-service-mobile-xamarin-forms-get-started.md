<properties
    pageTitle="Mise en route avec les applications Mobile à l’aide de Xamarin.Forms"
    description="Suivez ce didacticiel pour commencer à utiliser les applications Mobile Azure pour le développement de Xamarin.Forms"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-xamarinforms-app"></a>Créer une application de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment ajouter un service en nuage de back-end d’une application mobile Xamarin.Forms à l’aide d’un back-end de l’application Mobile de Azure. Vous allez créer un nouveau contexte d’application Mobile et d’une simple _liste Todo_ , app Xamarin.Forms qui stocke les données d’application dans Azure.

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels d’applications Mobile pour Xamarin.Forms.

##<a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez les éléments suivants :

* Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez vous inscrire pour une version d’évaluation d’Azure et obtenir jusqu'à 10 libre Apps Mobile que vous pouvez continuer à utiliser même après la fin de votre version d’évaluation. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio avec Xamarin. Pour obtenir des instructions, reportez-vous à la section [de configuration et l’installation de Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) . 

* Un Mac avec Xcode 7.0 ou version ultérieure et la Communauté de Studio Xamarin installé. Consultez [le programme d’installation et d’installation de Visual Studio et de Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) et [le programme d’installation, installation et les vérifications pour les utilisateurs de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile), où vous pouvez créer immédiatement un starter courte application Mobile dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un nouveau backend Azure Mobile App

Suivez ces étapes pour créer un nouveau contexte d’application Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Vous avez maintenant déployé un back-end Azure Mobile application qui peut être utilisé par les applications clientes mobiles. Ensuite, vous allez télécharger un projet de serveur pour une simple « liste todo » back-end et le publier dans Azure.

## <a name="configure-the-server-project"></a>Configurez le projet serveur

Suivez les étapes ci-dessous pour configurer le projet serveur pour utiliser back-end .NET ou Node.js.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-xamarinforms-solution"></a>Téléchargez et exécutez la solution Xamarin.Forms

Ici, vous avez deux options. Vous pouvez télécharger la solution sur un Mac et ouvrez-le dans Xamarin Studio, ou vous pouvez télécharger la solution sur un ordinateur Windows et l’ouvrir dans Visual Studio à l’aide d’un Mac en réseau pour la création de l’application iOS. Consultez [le programme d’installation et d’installation de Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) si vous avez besoin d’instructions plus détaillées sur les scénarios d’installation Xamarin.

Nous allons continuer :

 1. Sur votre Mac ou sur votre ordinateur Windows, ouvrez le [Portail Azure] dans une fenêtre de navigateur.
 2. Sur la lame de paramètres pour votre application Mobile, cliquez sur **Mise en route** (sous Mobile) > **Xamarin.Forms**. Sous étape 3, cliquez sur **créer une nouvelle application** si elle n’est pas déjà sélectionné.  Ensuite, cliquez sur le bouton **Télécharger** .

    Ceci permet de télécharger un projet qui contient une application cliente qui est connectée à votre application mobile. Enregistrer le fichier de projet compressés sur votre ordinateur local et prenez note de l’endroit où vous l’enregistrez.

 3. Extraire le projet que vous avez téléchargé, puis ouvrez-le dans Xamarin Studio ou Visual Studio.

    ![][9]

    ![][8]


##<a name="optional-run-the-ios-project"></a>(Facultatif) Exécutez le projet d’e/s

Cette section est pour le projet d’e/s de Xamarin pour les périphériques d’e/s en cours d’exécution. Vous pouvez ignorer cette section si vous ne travaillez pas avec les périphériques d’e/s.

####<a name="in-xamarin-studio"></a>Dans un Studio de Xamarin

1. Droit sur le projet d’e/s, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **exécuter** , cliquez sur **Démarrer le débogage** pour générer le projet et démarrer l’application dans l’émulateur iPhone.

####<a name="in-visual-studio"></a>Dans Visual Studio
1. Droit sur le projet d’e/s, puis cliquez sur **définir comme projet de démarrage**.
2. Dans le menu **Générer** , cliquez sur **Gestionnaire de Configuration**.
3. Dans la boîte de dialogue **Gestionnaire de configurations** , sélectionnez les cases à cocher de la **génération** et le **déploiement** du projet iOS.
4. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans l’émulateur iPhone.

    >[AZURE.NOTE] Si vous avez des problèmes de génération, exécutez NuGet du Gestionnaire de package et de la mise à jour vers la dernière version de la Xamarin prend en charge les packages. Parfois, les projets de démarrage rapide peuvent rester derrière dans la mise à jour avec les derniers fichiers.    

Dans l’application, tapez un texte explicite comme _Xamarin d’en savoir plus_ , puis cliquez sur le **+** bouton.

![][10]

Envoie une demande POST vers le nouveau back-end de mobile application hébergée dans Azure. Données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyées par le serveur principal d’une application mobile, et les données sont affichées dans la liste.

>[AZURE.NOTE]
> Vous trouverez le code qui accède à votre back-end de l’application mobile dans le fichier TodoItemManager.cs C# du projet de bibliothèque de classes portable de votre solution.

##<a name="optional-run-the-android-project"></a>(Facultatif) Exécutez le projet Android

Cette section est d’exécuter le projet droid de Xamarin pour Android. Vous pouvez ignorer cette section si vous ne travaillez pas avec des appareils Android.

####<a name="in-xamarin-studio"></a>Dans un Studio de Xamarin

1. Droit sur le projet Android, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **exécuter** , cliquez sur **Démarrer le débogage** pour générer le projet et démarrer l’application dans un émulateur Android.

####<a name="in-visual-studio"></a>Dans Visual Studio
1. Droit sur le projet Android (Droid), puis cliquez sur **définir comme projet de démarrage**.
4. Dans le menu **Générer** , cliquez sur **Gestionnaire de Configuration**.
5. Dans la boîte de dialogue **Gestionnaire de configurations** , sélectionnez les cases à cocher de la **génération** et le **déploiement** du projet Android.
6. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans un émulateur Android.

    >[AZURE.NOTE] Si vous avez des problèmes de génération, exécutez NuGet du Gestionnaire de package et de la mise à jour vers la dernière version de la Xamarin prend en charge les packages. Parfois, les projets de démarrage rapide peuvent rester derrière dans la mise à jour avec les derniers fichiers.    


Dans l’application, tapez un texte explicite comme _Xamarin d’en savoir plus_ , puis cliquez sur le **+** bouton.

![][11]

Envoie une demande POST vers le nouveau back-end de mobile application hébergée dans Azure. Données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyées par le serveur principal d’une application mobile, et les données sont affichées dans la liste.

> [AZURE.NOTE]
> Vous trouverez le code qui accède à votre back-end de l’application mobile dans le fichier TodoItemManager.cs C# du projet de bibliothèque de classes portable de votre solution.


##<a name="optional-run-the-windows-project"></a>(Facultatif) Exécutez le projet Windows


Cette section concerne l’exécution du projet Xamarin WinApp pour les périphériques Windows. Vous pouvez ignorer cette section si vous ne travaillez pas avec des périphériques Windows.


####<a name="in-visual-studio"></a>Dans Visual Studio
1. Cliquez sur un des projets Windows et puis cliquez sur **définir comme projet de démarrage**.
4. Dans le menu **Générer** , cliquez sur **Gestionnaire de Configuration**.
5. Dans la boîte de dialogue **Gestionnaire de Configuration** , sélectionnez les cases à cocher de la **génération** et le **déploiement** du projet Windows que vous avez choisi.
6. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans l’émulateur de Windows.

    >[AZURE.NOTE] Si vous avez des problèmes de génération, exécutez NuGet du Gestionnaire de package et de la mise à jour vers la dernière version de la Xamarin prend en charge les packages. Parfois, les projets de démarrage rapide peuvent rester derrière dans la mise à jour avec les derniers fichiers.    


Dans l’application, tapez un texte explicite comme _Xamarin d’en savoir plus_ , puis cliquez sur le **+** bouton.

Envoie une demande POST vers le nouveau back-end de mobile application hébergée dans Azure. Données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyées par le serveur principal d’une application mobile, et les données sont affichées dans la liste.

![][12]

> [AZURE.NOTE]
> Vous trouverez le code qui accède à votre back-end de l’application mobile dans le fichier TodoItemManager.cs C# du projet de bibliothèque de classes portable de votre solution.

##<a name="next-steps"></a>Étapes suivantes

* [Ajouter l’authentification à votre application](app-service-mobile-xamarin-forms-get-started-users.md)  
Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Ajouter des notifications de type pousser à votre application](app-service-mobile-xamarin-forms-get-started-push.md)  
Découvrez comment ajouter push notifications prennent en charge à votre application et configurez votre back-end de l’application Mobile pour utiliser des concentrateurs de Notification Azure pour envoyer des notifications de type Pousser.

* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Découvrez comment ajouter la prise en charge en mode hors connexion de votre application à l’aide d’un back-end de l’application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;affichage, ajout ou modification de données&mdash;même lorsqu’il n’y a pas de connexion réseau.

* [Comment faire pour utiliser le client géré pour les applications mobiles Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
Apprenez à travailler avec le SDK de client géré dans votre application de Xamarin. 


<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/

