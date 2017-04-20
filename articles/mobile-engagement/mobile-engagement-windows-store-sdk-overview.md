<properties
    pageTitle="Intégration de Windows SDK universel"
    description="Intégration de Windows universel pour le Kit de développement logiciel pour Azure Engagement Mobile"                                     
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
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

#<a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Intégration universel Kit de développement logiciel Windows Azure Engagement Mobile

Ce document décrit toutes les et options disponibles pour le SDK Azure Mobile Engagement Windows universel.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez d’abord terminer notre [didacticiel de 15 minutes](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Fonctionnalités avancées

### <a name="reporting-features"></a>Fonctions de Reporting
Vous pouvez ajouter ces fonctionnalités :

1. [Options de création de rapports avancées](mobile-engagement-windows-store-advanced-reporting.md)

2. [Options de configuration avancées](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notifications

[Comment intégrer la portée (Notifications) dans votre application Windows universel](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Mise en oeuvre du plan de balise :

[Comment faire pour utiliser l’Engagement Mobile avancée API de repérage dans votre application Windows universel](mobile-engagement-windows-store-use-engagement-api.md)

##<a name="release-notes"></a>Notes de publication

###<a name="340-04192016"></a>3.4.0 (19/04/2016)

-   Atteindre les améliorations de la superposition.
-   Journaux de console ajouté « TestLogLevel » API pour activer/désactiver/filtre émis par le Kit de développement logiciel.
-   Démarrer les notifications dans activité fixes ciblant la première activité ne s’affichée ne pas dans l’application.

Pour les versions antérieures, consultez les [notes de version complète](mobile-engagement-windows-store-release-notes.md)

##<a name="upgrade-procedures"></a>Procédures de mise à niveau

Si vous avez déjà intégré une version plus ancienne de l’Engagement dans votre application, vous devez considérer les points suivants lors de la mise à niveau de la SDK.

Si vous avez manqué plusieurs versions du Kit de développement, vous devrez peut-être suivre plusieurs procédures. Consultez [Procédures de mise à niveau](mobile-engagement-windows-store-upgrade-procedure.md)terminée. Par exemple, si vous migrez à partir de 0.10.1 vers 0.11.0, que vous devez tout d’abord suivre la procédure « à partir de 0.9.0 à 0.10.1 » puis la procédure « à partir de 0.10.1 à 0.11.0 ».

###<a name="from-330-to-340"></a>À partir de 3.3.0 à 3.4.0

####<a name="test-logs"></a>Journaux des tests

Journaux produits par le Kit de développement peuvent désormais être activé/désactivé/filtrées. Pour personnaliser, mettre à jour de la propriété `EngagementAgent.Instance.TestLogEnabled` à l’une des valeurs disponibles à partir de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

####<a name="resources"></a>Ressources

La superposition de portée a été améliorée. Il fait partie des ressources package NuGet du Kit de développement logiciel.

Lors de la mise à niveau vers la nouvelle version du Kit de développement, vous pouvez choisir si vous souhaitez conserver vos fichiers à partir du dossier de la superposition des ressources ou non :

* Si la superposition précédente fonctionne pour vous, ou si vous intégrez la `WebView` éléments manuellement, puis vous pouvez décider de conserver votre sortie de fichiers, il continuera de fonctionner.
* Pour mettre à jour à la superposition de nouveau, remplacez l’ensemble `overlay` dossier provenant de vos ressources avec une nouvelle à partir du package SDK (UWP applications : après la mise à niveau, vous pouvez obtenir le nouveau dossier de superposition %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] À l’aide de la nouvelle superposition remplace toutes les personnalisations apportées à la version précédente.

### <a name="upgrade-from-older-versions"></a>Mise à niveau à partir de versions antérieures

Consultez [les procédures de mise à niveau](mobile-engagement-windows-store-upgrade-procedure.md)
