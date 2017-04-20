<properties
    pageTitle="Mise en route avec Azure applications mobiles pour des applications Xamarin.Android"
    description="Suivez ce didacticiel pour commencer à utiliser les applications Mobile Azure pour le développement de Xamarin Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha" />

#<a name="create-a-xamarinandroid-app"></a>Créer une application de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel vous montre comment ajouter un service en nuage de back-end pour une application Xamarin.Android. Pour plus d’informations, voir [Quelles sont les applications mobiles](app-service-mobile-value-prop.md).

Une capture d’écran à partir de l’application terminée est inférieure à :

![][0]

À la fin de ce didacticiel est une condition préalable pour tous les autres didacticiels d’applications Mobile pour les applications Xamarin.Android.

##<a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez les conditions préalables suivantes :

* Un compte Azure actif. Si vous n’avez pas un compte, inscrivez-vous pour une version d’évaluation d’Azure et obtenir jusqu'à 10 applications Mobile libre. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio avec Xamarin. Pour obtenir des instructions, reportez-vous à la section [de configuration et l’installation de Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

>[AZURE.NOTE]Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile).  Vous pouvez créer immédiatement un starter courte application Mobile dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="create-an-azure-mobile-app-backend"></a>Créer un back-end de l’application Mobile de Azure

Suivez ces étapes pour créer un back-end de l’application Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant déployé un back-end Azure Mobile application qui peut être utilisé par les applications clientes mobiles. Ensuite, télécharger un projet de serveur pour une simple « liste todo » back-end et le publier dans Azure.

## <a name="configure-the-server-project"></a>Configurez le projet serveur

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Téléchargez et exécutez l’application Xamarin.Android

1. **Téléchargez et exécutez votre projet Xamarin.Android**, cliquez sur le bouton **Télécharger** .

    Enregistrer le fichier de projet compressés sur votre ordinateur local et prenez note de l’endroit où vous l’enregistrez.

2. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application.

3. Dans l’application, tapez un texte explicite comme _terminé ce didacticiel_ et puis cliquez sur le bouton **Ajouter** .

    ![][10]

    Données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyées par le serveur principal d’une application mobile, et les données s’affichent dans la liste.

    > [AZURE.NOTE] Vous pouvez consulter le code qui accède à votre back-end de l’application mobile pour les requêtes et insérer des données, qui se trouve dans le fichier ToDoActivity.cs de C#.

##<a name="next-steps"></a>Étapes suivantes

* [Ajouter la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Ajouter l’authentification à votre application](app-service-mobile-xamarin-android-get-started-users.md)
* [Ajouter des notifications de type pousser à votre application Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md)
* [Comment faire pour utiliser le client géré pour les applications mobiles Azure](app-service-mobile-dotnet-how-to-use-client-library.md)


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
