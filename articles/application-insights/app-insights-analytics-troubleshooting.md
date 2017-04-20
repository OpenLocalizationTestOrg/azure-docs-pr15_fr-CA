<properties 
    pageTitle="Résolution des problèmes d’Analytique - le puissant outil de recherche d’idées d’Application | Microsoft Azure" 
    description="Problèmes avec analytique d’idées d’Application ? Commencez ici. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/11/2016" 
    ms.author="awills"/>


# <a name="troubleshoot-analytics-in-application-insights"></a>Résoudre les problèmes d’Analytique dans les perspectives de l’Application


Problèmes avec [l’Application aperçu Analytique](app-insights-analytics.md)? Commencez ici. Analytique est un outil puissant de recherche d’idées d’Application Visual Studio.



## <a name="limits"></a>Limites

* À l’heure actuelle, les résultats de la requête sont limités à juste plus d’une semaine de données anciennes.
* Nous tester sur les navigateurs : dernières éditions de Chrome, Edge et Internet Explorer.


## <a name="known-incompatible-browser-extensions"></a>Extensions du navigateur incompatibles connus

* Ghostery

Désactiver l’extension, ou utilisez un autre navigateur.


##<a name="e-a"></a>« Erreur inattendue »

![Écran d’erreur inattendue](./media/app-insights-analytics-troubleshooting/010.png)

Une erreur interne s’est produite lors de l’exécution de portail – une exception non gérée.

* Nettoyer le cache du navigateur. 

## <a name="e-b"></a>403... Veuillez essayer de recharger

![403... Veuillez essayer de recharger](./media/app-insights-analytics-troubleshooting/020.png)

Une authentification concernant l’erreur s’est produite (lors de l’authentification ou pendant la génération de jeton d’accès). Le portail peut n’ont aucun moyen de récupérer sans modifier les paramètres du navigateur.

* Vérifiez [les cookies tiers sont activés](#cookies) dans le navigateur. 


## <a name="authentication"></a>403... Vérifiez la zone de sécurité

![403.. .verify zone de sécurité](./media/app-insights-analytics-troubleshooting/030.png)

Une authentification concernant l’erreur s’est produite (lors de l’authentification ou pendant la génération de jeton d’accès). Le portail peut n’ont aucun moyen de récupérer sans modifier les paramètres du navigateur.

1. Vérifiez [les cookies tiers sont activés](#cookies) dans le navigateur. 

2. Avez-vous utilisé un favori, un signet ou un lien enregistré pour ouvrir le portail Analytique ? Si vous êtes connecté avec différentes informations d’identification que vous avez utilisé lorsque vous avez enregistré le lien ?

2. Essayez d’utiliser une fenêtre de navigateur de privé/incognito (après avoir fermé toutes les fenêtres de ce type). Vous devrez fournir vos informations d’identification. 

2. Ouvrir une autre fenêtre de navigateur (ordinaire) et accédez à [Azure](https://portal.azure.com). Se déconnecter. Ouvrez ensuite votre lien et les signe avec les informations d’identification correctes.

2. Les utilisateurs de bord et d’Internet Explorer peuvent aussi obtenir cette erreur lorsque les paramètres de zone de confiance ne sont pas pris en charge.

    Vérifiez le [portail d’Analytique](https://analytics.applicationinsights.io) et de [portail d’Azure Active Directory](https://portal.azure.com) sont dans la même zone de sécurité :

 * Dans Internet Explorer, ouvrez **Options Internet**, **sécurité**, **sites de confiance**, **Sites**:

    ![Boîte de dialogue Options Internet, ajout d’un site aux Sites de confiance](./media/app-insights-analytics-troubleshooting/033.png)

    Dans la liste des sites Web, si une des URL suivantes sont incluse, assurez-vous que les autres sont également inclus :

    https://Analytics.applicationinsights.IO<br/>
   https://login.microsoftonline.com<br/>
   https://login.Windows.NET


## <a name="e-d"></a>404... Ressource non trouvée

![... les 404 Ressource introuvable](./media/app-insights-analytics-troubleshooting/040.png)

Ressource d’application a été supprimée à partir de l’aperçu de l’Application et n’est plus disponible. Cela peut se produire si vous avez enregistré l’URL vers la page Analytique.


## <a name="e-e"></a>403... Aucune autorisation

![403... ne pas autorisé](./media/app-insights-analytics-troubleshooting/050.png)

Vous n’êtes pas autorisé à ouvrir cette application dans Analytique.

* Vous avez reçu le lien à partir d’une autre personne ? Demandez-leur de vous assurer que vous êtes dans les [lecteurs ou les collaborateurs pour ce groupe de ressources](app-insights-resources-roles-access-control.md).
* Avez-vous enregistré le lien à l’aide de différentes informations d’identification ? Ouvrir le [portail Azure](https://portal.azure.com)et se déconnecter puis essayez à nouveau, ce lien fournissant les informations d’identification correctes.

## <a name="html-storage"></a>403... Stockage de HTML5

Notre portail utilise sessionStorage et localStorage de HTML5.

* Chrome : Paramètres, confidentialité, paramètres de contenu.
* Internet Explorer : Options Internet, onglet Avancé, sécurité, activer le stockage DOM


![403... essayez d’activer le stockage de HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404... Abonnement introuvable


![404... Abonnement introuvable](./media/app-insights-analytics-troubleshooting/070.png)

L’URL n’est pas valide. 

* Ouvrez la ressource d’application dans les [perspectives de l’Application portail](https://portal.azure.com). Utilisez le bouton Analytique.

## <a name="e-h"></a>404... n’existe pas

![404... Page n’existe pas](./media/app-insights-analytics-troubleshooting/080.png)

L’URL n’est pas valide.

* Ouvrez la ressource d’application dans les [perspectives de l’Application portail](https://portal.azure.com). Utilisez le bouton Analytique.

## <a name="cookies"></a>Activer les cookies tiers

  Voir [Comment faire pour désactiver les cookies tierce partie](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), mais notez que pour **Activer** leur utilité.

## <a name="e-x"></a>Si tout le reste échoue    

[Nous contacter](app-insights-get-dev-support.md).
 
[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

