<properties
    pageTitle="Configuration pour mission d’applications universel Windows SDK avancée"
    description="Options avancées de Configuration pour Azure l’Engagement Mobile avec applications universel de Windows"                    
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Configuration pour mission d’applications universel Windows SDK avancée

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)
- [e/s](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Cette procédure décrit comment configurer différentes options de configuration pour les applications Azure Mobile Engagement Android.

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

##<a name="advanced-configuration"></a>Configuration avancée

### <a name="disable-automatic-crash-reporting"></a>Désactiver le rapport d’incident automatique

Vous pouvez désactiver la fonctionnalité d’Engagement de rapport d’incident automatique. Ensuite, lorsqu’une exception non gérée se produit, Engagement n’a aucun effet.

> [AZURE.WARNING] Si vous désactivez cette fonctionnalité, puis lorsqu’un blocage non gérée se produit dans votre application, Engagement n’envoie pas que l’incident **et** ne ferme pas la session et les tâches.

Pour désactiver le rapport d’incident automatique, personnaliser votre configuration en fonction de la façon dont vous l’avez déclaré :

#### <a name="from-engagementconfigurationxml-file"></a>À partir de `EngagementConfiguration.xml` fichier

Blocage de rapport la valeur `false` entre `<reportCrash>` et `</reportCrash>` balises.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>À partir de `EngagementConfiguration` objet au moment de l’exécution

Blocage de rapport la valeur false à l’aide de l’objet EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Désactiver la création de rapports en temps réel

Par défaut, les rapports de service Engagement journaux en temps réel. Si votre application rapports journaux fréquemment, il est préférable pour les journaux de la mémoire tampon et de les signaler à la fois sur une base horaire normal. Ceci est appelé « mode rafale ».

Pour ce faire, appelez la méthode :

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L’argument est une valeur en **millisecondes**. Chaque fois que vous souhaitez réactiver l’enregistrement en temps réel, appelez la méthode sans paramètre ou avec la valeur 0.

Le mode rafale est légèrement augmente la durée de vie de la batterie mais a un impact sur le moniteur de l’Engagement : toutes les sessions et les travaux la durée sont arrondies à la valeur seuil de transmission en rafale (par conséquent, les sessions et les tâches plus courtes que le seuil de transmission en rafale ne soient pas visible). Nous vous recommandons d’utiliser un seuil de transmission en rafale ne sont plus à 30000 (30 s). Journaux enregistrés sont limités à 300 articles. Si l’envoi est trop long, vous pouvez perdre certains journaux.

> [AZURE.WARNING] Impossible de configurer le seuil de transmission en rafale pour une période inférieure à une seconde. Si vous procédez ainsi, le Kit de développement affiche une trace de l’erreur et rétablit automatiquement la valeur par défaut, zéro seconde. Cela déclenche le SDK pour signaler les journaux en temps réel.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
