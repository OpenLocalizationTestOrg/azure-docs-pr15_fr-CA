<properties
    pageTitle="Mise à jour des annotations pour les perspectives d’Application | Microsoft Azure"
    description="Ajouter le déploiement ou générer des marqueurs à vos graphiques d’explorer les mesures dans les perspectives de l’Application."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="awills"/>

# <a name="release-annotations-in-application-insights"></a>Annotations de version dans les perspectives de l’Application

Annotations sur les graphiques s’affichent [Les mesures de l’Explorateur](app-insights-metrics-explorer.md) dans lequel vous avez déployé une nouvelle version de la version. Ils facilitent voir si vos modifications ont un effet sur les performances de votre application. Ils peuvent être créés automatiquement par le [système de génération Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs), et vous pouvez également [les créer à partir de PowerShell](#create-annotations-from-powershell).

![Exemple d’annotations avec corrélation visible avec un temps de réponse de serveur](./media/app-insights-annotations/00.png)

Annotations de version sont une fonctionnalité de la version en nuage et de Visual Studio Team Services service de mise à jour. 

## <a name="install-the-annotations-extension-one-time"></a>Installer l’extension Annotations (une fois)

Pour être en mesure de créer des annotations de version, vous devez installer un des nombreux équipe extensions du Service disponibles sur le marché de Visual Studio.

1. Connectez-vous à votre projet [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) .
2. Dans Visual Studio Marketplace, [obtenir l’extension Annotations de version](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)et l’ajouter à votre compte de Services de l’équipe.

![Au haut à droite de la page web des Services de l’équipe, marché ouvert. Sélectionnez Visual Team Services, puis sous la version et de Build, consultez plus.](./media/app-insights-annotations/10.png)

Il vous suffit de vous faire une fois pour votre compte Services d’équipe Visual Studio. Annotations de version peuvent désormais être configurées pour n’importe quel projet de votre compte. 

## <a name="get-an-api-key-from-application-insights"></a>Obtenir une clé API à partir de l’aperçu de l’Application

Vous devez effectuer cette opération pour chaque modèle de la version que vous souhaitez créer des annotations de version.


1. Connectez-vous au [Portail de Microsoft Azure](https://portal.azure.com) et ouvrez la ressource d’idées d’Application qui surveille votre application. (Ou [en créer un maintenant](app-insights-overview.md), si vous n’avez pas encore fait).
2. Ouvrir **l’Accès à l’API**et prendre une copie de **l’Id de l’Application aperçu**.

    ![Dans portal.azure.com, ouvrez votre ressource de perspectives de l’Application et choisissez Paramètres. Ouvrir l’accès à l’API. Copiez l’ID de l’Application](./media/app-insights-annotations/20.png)

2. Dans une fenêtre distincte du navigateur, ouvrez (ou créez) le modèle de version qui gère vos déploiements de Visual Studio Team Services. 

    Ajouter une tâche et sélectionnez la tâche de l’Application aperçu version Annotation dans le menu.

    Collez l' **Id de l’Application** que vous avez copiés à partir de la blade d’accès à l’API.

    ![Dans Visual Studio Team Services, ouvrez la version, sélectionnez une définition de la version, puis sélectionnez Modifier. Cliquez sur Ajouter une tâche, puis sélectionnez Application Insights version Annotation. Collez l’ID de perspectives d’Application.](./media/app-insights-annotations/30.png)

3. Une variable de la valeur du champ **APIKey** `$(ApiKey)`.

4. Dans la blade d’accès à l’API, créez une nouvelle clé d’API et prendre une copie de celui-ci.

    ![De la lame d’accès à l’API dans la fenêtre d’Azure, cliquez sur Créer une clé API. Fournir un commentaire, vérifiez les annotations de l’écriture, cliquez sur Générer la clé. Copier la nouvelle clé.](./media/app-insights-annotations/40.png)

4. Ouvrez l’onglet Configuration du modèle de publication.

    Créer une définition de variable pour `ApiKey`.

    Collez la clé d’API pour la définition de la variable ApiKey.

    ![Dans la fenêtre Team Services, sélectionnez l’onglet Configuration et cliquez sur Ajouter une Variable. Définir le nom ApiKey et dans la valeur, collez la clé que vous venez de générer.](./media/app-insights-annotations/50.png)


5. Enfin, les **Enregistrer** la définition de la version.

## <a name="create-annotations-from-powershell"></a>Créer des annotations à partir de PowerShell

Vous pouvez également créer des annotations de tout processus que vous le souhaitez (sans l’aide de VS Team System). 

Obtenez le [script Powershell à partir de GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

Utiliser comme suit :

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Obtenir le `applicationId` et une `apiKey` à partir de votre ressource de perspectives d’Application : ouvrir les paramètres, accès à l’API et copier le code de l’Application. Cliquez sur Créer une clé API, puis copiez la clé. 

## <a name="release-annotations"></a>Annotations de version

Maintenant, chaque fois que le modèle de version vous permet de déployer une nouvelle version, une annotation sera envoyée aux analyses de l’Application. Les annotations seront affiche sur les graphiques dans l’Explorateur de mesures.

Cliquez sur n’importe quel marqueur d’annotation pour ouvrir plus d’informations sur la version, y compris le demandeur, la branche de contrôle de code source, relâchez la définition, l’environnement et bien plus encore.


![Cliquez sur n’importe quel marqueur d’annotation de version.](./media/app-insights-annotations/60.png)
