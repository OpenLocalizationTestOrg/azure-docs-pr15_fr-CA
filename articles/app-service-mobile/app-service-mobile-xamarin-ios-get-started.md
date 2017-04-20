<properties
    pageTitle="Mise en route avec le Service Azure App Apps Mobile pour les applications Xamarin.iOS | Microsoft Azure"
    description="Suivez ce didacticiel mise en route à l’aide d’applications Mobile pour le développement de Xamarin.iOS."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#<a name="create-a-xamarinios-app"></a>Créer une application de Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment ajouter un service en nuage de back-end d’une application mobile Xamarin.iOS à l’aide d’un back-end de l’application mobile Azure.  Vous créez un nouveau contexte d’application mobile et une simple _liste Todo_ app Xamarin.iOS qui stocke les données d’application dans Azure.

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels Xamarin.iOS sur l’utilisation de la fonctionnalité d’applications Mobile dans le Service d’application Azure.

##<a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez les conditions préalables suivantes :

* Un compte Azure actif. Si vous n’avez pas un compte, inscrivez-vous pour un essai Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pouvez continuer à utiliser même après la fin de votre version d’évaluation. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio avec Xamarin. Pour obtenir des instructions, reportez-vous à la section [de configuration et l’installation de Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

* Un Mac avec Xcode 7.0 ou version ultérieure et la Communauté de Studio Xamarin installé. Consultez [le programme d’installation et d’installation de Visual Studio et de Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) et [le programme d’installation, installation et les vérifications pour les utilisateurs de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE]Si vous souhaitez commencer avec le Service d’application Azure avant de vous inscrivez pour un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile). Vous pouvez créer immédiatement une application mobile starter de courte durée dans le Service d’application : aucune carte de crédit requise et aucun des engagements.

## <a name="create-an-azure-mobile-app-backend"></a>Créer un back-end de l’application Mobile de Azure

Suivez ces étapes pour créer un back-end de l’application Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurez le projet serveur

Vous avez maintenant déployé un back-end Azure Mobile application qui peut être utilisé par les applications clientes mobiles. Ensuite, télécharger un projet de serveur pour une simple « liste todo » back-end et le publier dans Azure.

Suivez les étapes suivantes pour configurer le projet serveur pour utiliser back-end .NET ou Node.js.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Téléchargez et exécutez l’application Xamarin.iOS

1. Ouvrez le [portail Azure] dans une fenêtre de navigateur.

2. Sur la lame de paramètres pour votre application Mobile, cliquez sur **Mise en route** > **Xamarin.iOS**. Sous étape 3, cliquez sur **créer une nouvelle application** si elle n’est pas déjà sélectionné.  Ensuite, cliquez sur le bouton **Télécharger** .

    Une application cliente qui se connecte à votre serveur principal mobile est téléchargée. Enregistrer le fichier de projet compressés sur votre ordinateur local et prenez note de l’endroit où vous l’enregistrez.

3. Extraire le projet que vous avez téléchargé, puis ouvrez-le dans Xamarin Studio (ou Visual Studio).

    ![][9]

    ![][8]

4. Appuyez sur la touche F5 pour générer le projet et démarrer l’application dans l’émulateur iPhone.

5. Dans l’application, tapez un texte explicite comme _Xamarin d’en savoir plus_et puis cliquez sur le **+** bouton.

    ![][10]

    Données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyées par le serveur principal d’une application mobile, et les données sont affichées dans la liste.

>[AZURE.NOTE]Vous pouvez consulter le code qui accède à votre back-end de l’application mobile pour les requêtes et insérer des données dans le fichier QSTodoService.cs de C#.

##<a name="next-steps"></a>Étapes suivantes

* [Ajouter la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Ajouter l’authentification à votre application](app-service-mobile-xamarin-ios-get-started-users.md)
* [Ajouter des notifications de type pousser à votre application Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Comment faire pour utiliser le client géré pour les applications mobiles Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure portal]: https://portal.azure.com/
