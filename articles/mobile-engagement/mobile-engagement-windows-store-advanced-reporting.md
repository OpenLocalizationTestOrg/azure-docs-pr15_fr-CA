<properties
    pageTitle="Universel de Windows avancée de création de rapports avec l’Engagement de MobileApps"
    description="Comment intégrer applications universel de Windows Azure Engagement Mobile"                  
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Rapports avec l’Engagement de Universal applications Windows SDK avancée

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-advanced-reporting.md)
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)
- [e/s](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Cette rubrique décrit des scénarios de création de rapports supplémentaires dans votre application Windows universel. Ces scénarios comprennent des options que vous pouvez choisir d’appliquer à l’application créée dans le didacticiel [Mise en route](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Avant de commencer ce didacticiel, vous devez d’abord effectuer le didacticiel [Mise en route](mobile-engagement-windows-store-dotnet-get-started.md) , qui est délibérément simple et directe. Ce didacticiel couvre les options supplémentaires que vous pouvez choisir à partir de.

## <a name="specifying-engagement-configuration-at-runtime"></a>Spécifier la configuration d’engagement lors de l’exécution

La configuration de l’Engagement est centralisée dans la `Resources\EngagementConfiguration.xml` fichier de votre projet, qui est où il a été spécifié dans la rubrique [Mise en route](mobile-engagement-windows-store-dotnet-get-started.md) .

Mais vous pouvez également le spécifier lors de l’exécution : vous pouvez appeler la méthode suivante avant l’initialisation de l’agent d’Engagement :

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Méthode recommandée : surcharger votre `Page` classes

Pour activer la génération d’états de tous les journaux requis par l’Engagement pour calculer les utilisateurs, les Sessions, les activités, les pannes et les techniques statistiques, vérifiez tous les votre `Page` sous-classes héritent de le `EngagementPage` classes.

Voici un exemple d’une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

### <a name="c-source-file"></a>Fichier Source C#

Modifier votre page `.xaml.cs` fichier :

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Remplacer `Page` avec `EngagementPage`:

**Sans Engagement :**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Avec Engagement :**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] Si votre page remplace la `OnNavigatedTo` méthode, veillez à appeler `base.OnNavigatedTo(e)`. Dans le cas contraire, l’activité n’est pas être signalée (le `EngagementPage` appelle `StartActivity` à l’intérieur de sa `OnNavigatedTo` méthode).

### <a name="xaml-file"></a>Fichier XAML

Modifier votre page `.xaml` fichier :

-   Ajoutez à vos déclarations d’espaces de noms :

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Remplacer `Page` avec `engagement:EngagementPage`:

**Sans Engagement :**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Avec Engagement :**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Substituer le comportement par défaut

Par défaut, le nom de classe de la page est signalé en tant que nom de l’activité, aucun extra. Si la classe utilise le suffixe « Page », l’Engagement le supprime.

Pour substituer le comportement par défaut pour le nom, ajoutez le code suivant :

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Pour signaler des informations supplémentaires relatives à votre activité, ajoutez le code suivant :

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Ces méthodes sont appelées à partir du `OnNavigatedTo` méthode de votre page.

### <a name="alternate-method-call-startactivity-manually"></a>Méthode alternative : appelez `StartActivity()` manuellement

Si vous ne pouvez pas ou ne voulez pas surcharger votre `Page` classes, vous pouvez démarrer à la place de vos activités en appelant `EngagementAgent` méthodes directement.

Nous vous recommandons d’appeler `StartActivity` à l’intérieur de votre `OnNavigatedTo` méthode de votre Page.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Assurez-vous que vous terminez votre session correctement.
>
> Le Kit de développement logiciel universel Windows appelle automatiquement le `EndActivity` méthode lors de la fermeture de l’application. Par conséquent, il est **vivement** recommandé d’appeler le `StartActivity` méthode chaque fois que l’activité de l’utilisateur modifier, **jamais** appeler le `EndActivity` méthode. Cette méthode avertit le serveur de l’Engagement que l’utilisateur actuel a quitté l’application, ce qui aura un impact sur tous les journaux de l’application.

## <a name="advanced-reporting"></a>Reporting avancé

Si vous le souhaitez, vous souhaitez signaler des événements d’application, des erreurs et des tâches, pour ce faire, utilisent les autres méthodes se trouvent dans le `EngagementAgent` classe. L’API d’Engagement permet d’utiliser des fonctions avancées de tout Engagement.

Pour plus d’informations, voir [comment utiliser l’Engagement Mobile avancée API dans votre application Windows universel de marquage](mobile-engagement-windows-store-use-engagement-api.md).
